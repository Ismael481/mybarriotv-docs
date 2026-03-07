# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` operativo.
- `TASK_006` cerrada e implementada.
- `TASK_007` cerrada como diseno.
- `TASK_008` cerrada e implementada.
- `TASK_009` cerrada e implementada/validada.
- `TASK_010` implementada y en validacion final del usuario.

## Auth actual
### Core
- `POST /v1/auth/login`
- `GET /v1/auth/me`
- `GET /v1/auth/protected`

### Device login QR
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`

### Device binding
- `GET /v1/auth/devices`
- `POST /v1/auth/devices/revoke`
- Estado: `active` / `revoked`

## Web auth surface (TASK_010)
- Superficie web minima formalizada en `apps/web-app/public/auth`:
  - `/auth/login`
  - `/auth/device?sessionId=...`
  - `/auth/register`
- UI web auth actualizada a estilo moderno/elegante manteniendo la misma logica de flujo.
- Backend mantiene auth API central.
- TV login manual y QR se mantienen sin cambios de UI.

## Persistencia y hardening activos
- Store JSON persistente para sesiones/devices (`AUTH_STORE_FILE`).
- Rate limiting basico en `start`, `approve`, `exchange`.
- Auditoria basica en store.

## Riesgos actuales
- Store JSON no es DB productiva.
- Registro web aun base visual/funcional (sin OTP real).
