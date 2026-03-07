# CHATGPT_CONTEXT

Fecha: 2026-03-07  
Rama: `main`  
Tarea activa: `TASK_009_auth_persistence_and_device_binding_hardening`

## Estado corto
- TASK_008 ya esta implementada y estable (manual + QR + aprobacion web + exchange).
- TASK_009 implementa persistencia y hardening basico de auth/device.

## Cambios tecnicos clave de TASK_009
- Nuevo store persistente: `backend/src/authPersistence.js`.
- Persistencia de:
  - `deviceLoginSessions`
  - `linkedDevicesByUser`
  - `auditEvents`
- Reglas de vinculacion:
  - deduplicacion por `deviceKey`
  - estado `active` / `revoked`
  - bloqueo de login/exchange para dispositivo revocado
- Endpoint nuevo:
  - `POST /v1/auth/devices/revoke`
- Anti abuso:
  - rate limit en `start`, `approve`, `exchange`

## Archivos clave modificados
- `backend/src/authPersistence.js`
- `backend/src/deviceLogin.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `docs/02_tasks/TASK_009_auth_persistence_and_device_binding_hardening.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Cambios manuales externos
- No se requieren cambios en XUI para TASK_009.
- Configurar env backend:
  - `AUTH_STORE_FILE`
  - `AUTH_DEVICE_SESSION_TTL_SECONDS`
  - `AUTH_DEVICE_SESSION_RETENTION_SECONDS`
  - `AUTH_RL_DEVICE_*`

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_009_auth_persistence_and_device_binding_hardening.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
