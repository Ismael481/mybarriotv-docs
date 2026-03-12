# TASK_066_auth_device_session_db_foundation

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Migrar de forma incremental a DB la persistencia critica de sesiones QR, vinculos de dispositivos y auditoria auth/device, manteniendo compatibilidad temporal con el flujo actual.

## Alcance
- Auditoria previa de `deviceLoginSessions`, `linkedDevicesByUser` y `auditEvents`.
- Extension minima de SQLite operativa para auth/device/session.
- Persistencia DB minima real para:
  - sesiones de login QR por dispositivo;
  - dispositivos vinculados por cuenta;
  - eventos auth/device.
- Lectura preferente desde DB cuando aplica, con fallback seguro a JSON.
- Compatibilidad preservada para login manual, login QR, revocacion de dispositivos y TV app.

## Fuera de alcance
- Billing.
- Migracion total de auth/otp/sesiones en una sola tarea.
- Refactor amplio.
- Panel admin grande.
- Multi-servidor.
- Eliminacion inmediata de `AUTH_STORE_FILE`.
- Cambios grandes en TV app.

## Auditoria resumida previa
- `deviceLoginSessions` seguia persistiendo solo en `AUTH_STORE_FILE`.
- `linkedDevicesByUser` ya se espejaba hacia `operational_linked_devices`, pero la lectura operativa seguia saliendo del store JSON.
- `auditEvents` ya se insertaba en `operational_events`, pero listados operativos seguian leyendo el arreglo JSON historico.
- Existia ademas compatibilidad legacy en fixtures/store donde `linkedDevicesByUser` podia estar indexado por `accountId` sin prefijo `account:`.

## Implementacion minima aplicada
- `backend/src/operationalDb.js`
  - Nueva tabla `operational_device_login_sessions`.
  - Nuevos accesos:
    - `upsert/get/list/deleteOperationalDeviceLoginSession`
    - `listOperationalLinkedDevices`
    - `listOperationalEvents`
  - `bootstrapOperationalFromAuthStore` ahora siembra tambien sesiones QR desde JSON.
  - `appendOperationalEvent` deriva `account_id` desde `userId=account:*` cuando el evento no lo trae explicito.
- `backend/src/authPersistence.js`
  - `deviceLoginSessions` pasa a dual-write JSON + DB.
  - Lectura de sesiones QR usa DB preferente con fallback JSON.
  - Lectura de dispositivos vinculados usa DB preferente con fallback JSON.
  - Lectura de auditoria auth/device usa DB preferente con fallback JSON.
  - Compatibilidad legacy para `linkedDevicesByUser` por `accountId` o `account:<id>`.
- `backend/src/server.js`
  - Detalle ops de cuenta (`recentAudit`) usa el acceso filtrado nuevo en lugar de filtrar solo el arreglo JSON.

## Que queda ya en DB
- Sesiones QR:
  - estado (`pending|approved|denied|expired`)
  - `createdAtMs`, `expiresAtMs`, `approvedAtMs`, `deniedAtMs`, `exchangedAtMs`, `expiredAtMs`
  - aprobador (`approvedBy`) y `account_id`
  - `created_by_device_key`
- Vinculos de dispositivos:
  - lectura operativa preferente desde `operational_linked_devices`
  - mutaciones siguen en convivencia temporal JSON + DB
- Auditoria auth/device:
  - lectura operativa preferente desde `operational_events`
  - eventos auth/device quedan consultables por cuenta/usuario

## Convivencia temporal que sigue activa
- `AUTH_STORE_FILE` sigue siendo capa compatible para:
  - fallback de sesiones QR si DB no esta disponible;
  - fallback de dispositivos vinculados si DB no tiene fila util;
  - fallback de auditoria si DB no esta disponible.
- No se elimina JSON en esta tarea.
- No se migra OTP ni el resto de dominios auth fuera del alcance pedido.

## Archivos tocados
- `backend/src/operationalDb.js`
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/02_tasks/TASK_066_auth_device_session_db_foundation.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos y limites residuales
- `node:sqlite` sigue siendo experimental en Node.
- Sigue existiendo convivencia JSON + DB; no hay corte definitivo de store historico.
- La lectura DB preferente depende de que la DB siga habilitada (`AUTH_OPERATIONAL_DB_ENABLED=true`).

## Pendiente de prueba manual
- Verificar en entorno operativo real que una sesion QR creada/aprobada desde TV + web deja rastro visible en `AUTH_OPERATIONAL_DB_FILE` y que la revocacion de dispositivo sigue estable sin depender solo de JSON.

## Resultado esperado
- Sesiones QR, dispositivos vinculados y eventos auth/device quedan persistidos de forma minima real en SQLite.
- Los flujos actuales continuan operativos.
- La convivencia temporal JSON + DB queda explicitamente documentada.

## Pasos manuales si existen
1. Confirmar `AUTH_OPERATIONAL_DB_ENABLED=true`.
2. Confirmar ruta persistente para `AUTH_OPERATIONAL_DB_FILE`.
3. Mantener backup de `AUTH_STORE_FILE` durante la fase de convivencia.

## Cambios externos XUI/config requeridos
- No hay cambios nuevos requeridos en XUI para esta tarea.
- Se mantiene el pendiente externo previo fuera del repo: rotacion/validacion de `XUI_ADMIN_API_KEY`.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`18` tests, `0` fallos).
- Evidencia trazable nueva:
  - `auth/device/session foundation persists QR session, linked device and auth event in DB`
  - valida:
    1. sesion QR persistida en `operational_device_login_sessions`;
    2. device vinculado persistido en `operational_linked_devices`;
    3. evento auth/device persistido en `operational_events`.

## Estado de cierre formal
- Objetivo y criterio de exito de `TASK_066` cumplidos.
- `TASK_066_auth_device_session_db_foundation` cerrada.
