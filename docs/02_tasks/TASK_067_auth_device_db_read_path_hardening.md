# TASK_067_auth_device_db_read_path_hardening

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Endurecer las rutas de lectura y operacion de auth/device/session para que SQLite sea la fuente preferente, manteniendo fallback controlado mientras dure la transicion.

## Alcance
- Auditoria de endpoints y helpers que aun cargaban primero el store JSON.
- Cambio de lectura preferente a DB para:
  - estado de sesion QR;
  - listado de dispositivos vinculados;
  - validacion de dispositivos revocados;
  - auditoria basica auth/device.
- Hardening minimo de mutaciones de dispositivo para no depender de que el JSON ya tenga la fila.
- Documentacion explicita de precedencia DB vs fallback JSON.

## Fuera de alcance
- Eliminar `AUTH_STORE_FILE`.
- Billing.
- Refactor amplio.
- Rediseño de endpoints.
- Cambios grandes en TV app.

## Auditoria resumida previa
- `getDeviceLoginSessionById`, `listDeviceLoginSessions`, `getLinkedDevicesForUser` y `listRecentAuditEvents` seguian llamando `getStore()` antes de intentar leer SQLite.
- Eso mantenia dependencia operativa innecesaria del store historico aun cuando la DB ya tenia los datos.
- `revokeLinkedDeviceForUser` y `setLinkedDeviceAccessStatus` seguian mutando partiendo solo del array JSON; si la fila existia en DB pero faltaba en JSON, la operacion podia fallar.
- La validacion de dispositivos revocados en `accessDecision` depende de `getLinkedDeviceByKey`, por lo que ese endurecimiento impacta login manual y QR exchange.

## Implementacion minima aplicada
- `backend/src/authPersistence.js`
  - Lectura DB primero real para:
    - `getDeviceLoginSessionById`
    - `listDeviceLoginSessions`
    - `getLinkedDevicesForUser`
    - `listRecentAuditEvents`
  - Fallback a JSON solo cuando DB no devuelve dato util.
  - Nuevo helper interno `getLinkedDevicesForMutation` para que mutaciones de dispositivos se apoyen en DB cuando el JSON ya no tenga la fila.
  - `upsertLinkedDeviceForUser`, `revokeLinkedDeviceForUser` y `setLinkedDeviceAccessStatus` ya no dependen de que `linkedDevicesByUser` exista primero en `AUTH_STORE_FILE`.
- No se cambian contratos HTTP ni payloads externos.

## Que rutas quedan endurecidas con DB preferente
- `GET /v1/auth/device/status/:sessionId`
  - resuelve estado de sesion QR desde `operational_device_login_sessions` primero.
- `GET /v1/auth/devices`
  - lista dispositivos desde `operational_linked_devices` primero.
- Validacion de dispositivo revocado/bloqueado:
  - `GET /v1/auth/access`
  - login manual y flujo QR exchange que reutilizan `accessDecision`
  - fuente preferente: `operational_linked_devices`.
- Auditoria auth/device operativa:
  - `GET /v1/auth/ops/accounts/:accountId` (`recentAudit`)
  - fuente preferente: `operational_events`.
- Operaciones de cambio de estado/revocacion de dispositivos:
  - `POST /v1/auth/devices/revoke`
  - `POST /v1/auth/ops/accounts/:accountId/devices/:deviceKey/status`
  - pueden reconstruir el estado desde DB aunque el JSON no tenga la entrada local.

## Cuando sigue aplicando fallback JSON
- Si SQLite esta deshabilitada o no disponible.
- Si SQLite no devuelve fila util para la sesion/dispositivo/evento consultado.
- `AUTH_STORE_FILE` sigue manteniendose como capa compatible durante la transicion.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/02_tasks/TASK_067_auth_device_db_read_path_hardening.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos y limites residuales
- La convivencia JSON + DB sigue activa por compatibilidad.
- `node:sqlite` sigue siendo experimental en Node.
- La eliminacion definitiva del store historico queda fuera de esta tarea.

## Pendiente de prueba manual
- Validar en entorno real que, con reinicio del backend y store historico degradado, siguen operativos:
  - consulta de QR pendiente/aprobado;
  - listado de TVs vinculadas;
  - revocacion de TV;
  - detalle ops con auditoria auth/device.

## Resultado esperado
- Los flujos criticos auth/device/session leen primero desde DB.
- Revocacion, consulta de dispositivos y sesiones QR siguen funcionando.
- Queda documentado que rutas ya dependen operativamente de DB y donde persiste el fallback.

## Pasos manuales si existen
1. Confirmar `AUTH_OPERATIONAL_DB_ENABLED=true`.
2. Mantener `AUTH_OPERATIONAL_DB_FILE` en ruta persistente.
3. Mantener backup de `AUTH_STORE_FILE` durante la transicion.

## Cambios externos XUI/config requeridos
- No hay cambios externos nuevos para esta tarea.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`20` tests, `0` fallos).
- Evidencia trazable agregada:
  - `device status resolves from DB when JSON session store entry is missing`
  - `device list, revoke and auth/device audit keep working from DB when JSON fallback data is absent`

## Estado de cierre formal
- Objetivo y criterio de exito de `TASK_067` cumplidos.
- `TASK_067_auth_device_db_read_path_hardening` cerrada.
