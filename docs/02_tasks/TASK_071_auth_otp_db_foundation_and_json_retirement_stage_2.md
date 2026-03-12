# TASK_071_auth_otp_db_foundation_and_json_retirement_stage_2

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Llevar a DB la persistencia critica restante del dominio auth/OTP para reducir la dependencia real del store JSON historico sin romper los flujos existentes de registro, login, OTP y recuperacion.

## Alcance
- Auditoria previa del dominio auth/OTP residual en backend.
- Extension minima de SQLite operativa para requests auth/OTP.
- Persistencia DB minima real para:
  - requests OTP de registro;
  - reset de contrasena por OTP;
  - OTP de cambio de contrasena/telefono de cuenta;
  - lectura/mutacion preferente de esas requests desde DB.
- Compatibilidad temporal controlada con JSON mientras dure la transicion.
- Documentacion explicita de precedencia DB vs JSON y de lo que aun falta para retiro final.

## Fuera de alcance
- Billing.
- Eliminacion fisica inmediata de `AUTH_STORE_FILE`.
- Refactor masivo de todo auth.
- Rediseno de contratos HTTP.
- Migracion total de cuentas/perfiles base a DB.
- Cambios grandes en TV app o panel admin.

## Auditoria resumida previa
- `registrationOtp` seguia dependiendo de `createOtpRequest`, `getOtpRequestById`, `updateOtpRequest` y `listOtpRequests`, todos sustentados solo por `AUTH_STORE_FILE`.
- `passwordResetOtp` seguia dependiendo de `createPasswordResetRequest`, `getPasswordResetRequestById` y `updatePasswordResetRequest`, tambien solo en JSON.
- `accountChangeOtp` reutilizaba el mismo patron residual con `accountChangeRequestsById`.
- La auditoria auth/OTP ya dejaba eventos en `operational_events`, pero el estado operativo de las requests OTP seguia sin una persistencia DB seria.
- El mayor impacto minimo estaba en mover el estado de requests auth/OTP a SQLite sin abrir una migracion total de cuentas.

## Implementacion minima aplicada
- `backend/src/operationalDb.js`
  - Nueva tabla `operational_auth_requests`.
  - Nuevos accesos:
    - `upsertOperationalAuthRequest`
    - `getOperationalAuthRequest`
    - `listOperationalAuthRequests`
    - `deleteOperationalAuthRequest`
  - `bootstrapOperationalFromAuthStore` ahora siembra tambien:
    - `otpRequestsById`
    - `passwordResetRequestsById`
    - `accountChangeRequestsById`
- `backend/src/authPersistence.js`
  - Nueva capa reusable para requests auth/OTP con store JSON + DB:
    - create/get/update/list/cleanup genericos
    - lectura preferente desde DB
    - mutacion capaz de reconstruirse desde DB si la fila JSON ya no existe
  - Aplicada a:
    - `otpRequestsById`
    - `passwordResetRequestsById`
    - `accountChangeRequestsById`
- `backend/test/minimum-foundation.test.js`
  - Caso nuevo para registro OTP persistido en DB y `verify/complete` funcionando tras retirar la fila del JSON y reiniciar backend.
  - Caso nuevo para password reset persistido en DB y `verify/complete` funcionando tras retirar la fila del JSON y reiniciar backend.
- `backend/README.md`
  - Precedencia DB actualizada para auth/device/session/otp.

## Que parte de auth/OTP queda ya sostenida por DB
- Requests de registro OTP:
  - `requestId`
  - `phone`
  - `username`
  - `status`
  - `createdAtMs`, `expiresAtMs`, `verifiedAtMs`, `completedAtMs`, `expiredAtMs`
  - `attempts`, `maxAttempts`
  - payload completo en `raw_json`
- Requests de reset de contrasena por OTP:
  - `requestId`
  - `accountId`
  - `phone`
  - `status`
  - timestamps/attempts
  - payload completo en `raw_json`
- Requests OTP de cambio de cuenta:
  - `type=password|phone`
  - `accountId`
  - `phone`
  - `status`
  - timestamps/attempts
  - payload completo en `raw_json`
- Auth/OTP audit:
  - los eventos ya siguen quedando en `operational_events`

## Precedencia actual DB vs JSON
- Fuente preferente operativa para requests auth/OTP:
  - SQLite `AUTH_OPERATIONAL_DB_FILE`
  - tabla `operational_auth_requests`
- Papel actual de `AUTH_STORE_FILE` para esta parte:
  - compatibilidad transitoria;
  - respaldo auxiliar;
  - fallback controlado si SQLite no esta disponible o una fila aun no esta en DB.
- En mutaciones de auth/OTP, si JSON no tiene ya la request pero DB si, el backend puede continuar desde DB y rehidratar la capa compatible.

## Que falta para retiro definitivo del store historico
- Retirar el dual-write de:
  - `otpRequestsById`
  - `passwordResetRequestsById`
  - `accountChangeRequestsById`
- Revisar y cerrar el uso residual de JSON para identidad base de cuenta/auth fuera de estas requests.
- Ejecutar una tarea posterior especifica de retiro final de `AUTH_STORE_FILE`, sin mezclarla con billing ni con refactor amplio.

## Archivos tocados
- `backend/src/operationalDb.js`
- `backend/src/authPersistence.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `backend/.env.example`
- `docs/02_tasks/TASK_071_auth_otp_db_foundation_and_json_retirement_stage_2.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos y limites residuales
- `node:sqlite` sigue siendo experimental en Node.
- Sigue existiendo convivencia JSON + DB; esta tarea no elimina fisicamente `AUTH_STORE_FILE`.
- La cuenta base y otros dominios auth fuera de estas requests todavia no completan retiro total de JSON.

## Pendiente de prueba manual
- Validar en entorno operativo real un ciclo de registro OTP y uno de reset por OTP con SMS real, reinicio de backend y verificacion del rastro en `AUTH_OPERATIONAL_DB_FILE`.

## Resultado esperado
- Registro OTP, reset por OTP y cambio OTP de cuenta quedan sostenidos por persistencia DB minima real.
- Los flujos existentes siguen funcionando.
- El store JSON historico queda aun mas relegado en auth/OTP y queda documentado lo que falta para retirarlo del todo.

## Pasos manuales si existen
1. Confirmar `AUTH_OPERATIONAL_DB_ENABLED=true`.
2. Mantener `AUTH_OPERATIONAL_DB_FILE` en ruta persistente con backup regular.
3. Mantener backup de `AUTH_STORE_FILE` mientras siga la convivencia.
4. Verificar en runtime real que el SMS provider responda y que el request OTP quede visible en SQLite.

## Cambios externos XUI/config requeridos
- No hay cambios nuevos requeridos en XUI para esta tarea.
- No hay configuracion externa nueva obligatoria fuera del repo.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`23` tests, `0` fallos).
- Evidencia trazable agregada:
  - `registration OTP request persists in DB and verify/complete survive JSON removal`
  - `password reset OTP persists in DB and complete works after JSON removal`

## Estado de cierre formal
- Objetivo y criterio de exito de `TASK_071` cumplidos.
- `TASK_071_auth_otp_db_foundation_and_json_retirement_stage_2` cerrada.
