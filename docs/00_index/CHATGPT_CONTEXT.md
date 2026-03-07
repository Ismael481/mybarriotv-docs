# CHATGPT_CONTEXT

Fecha: 2026-03-07
Rama: `main`
Tarea activa: `TASK_013_account_and_device_access_management_minimum`

## Resumen operativo
- TASK_013 hace operable el gate de TASK_012 sin editar JSON manual.
- Backend agrega endpoints ops minimos para listar cuentas, ver detalle y cambiar estados de cuenta/dispositivo.
- Web `mode=profile` agrega panel minimo de operacion para operador (`user:*`) manteniendo UX existente.

## Cambios clave recientes
- Backend:
  - `listAccounts()` en store.
  - nuevas rutas `/v1/auth/ops/accounts*`.
  - actualizacion de estados via `updateAccountStatus` y `setLinkedDeviceAccessStatus`.
  - auditoria con actor/timestamp para cambios ops.
  - env nuevo: `AUTH_OPS_ALLOWED_SUBS` (default `user:demo`).
- Web:
  - panel `Operacion de acceso (minima)` embebido en perfil.
  - buscar cuentas, ver detalle, cambiar `accountStatus`, cambiar `accessStatus` por TV.
  - estilos nuevos acotados en `v34-custom.css` siguiendo paleta actual.

## Pruebas tecnicas ejecutadas
- `node --check backend/src/server.js` OK.
- `node --check backend/src/authPersistence.js` OK.
- Smoke test backend en puerto 18081:
  - login demo operador
  - listado ops
  - detalle cuenta
  - cambio accountStatus
  - cambio device accessStatus
  - todos los pasos en OK

## Cambios manuales externos
- Ninguno.
- No hay cambios requeridos en XUI para TASK_013.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_013_account_and_device_access_management_minimum.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
