# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` operativo y estable.
- `TASK_006_authentication_foundation` cerrada como implementada.
- `TASK_007_auth_experience_and_device_login_design` cerrada como blueprint documental.
- `TASK_008_qr_device_login_implementation` implementada y activa para validacion final.

## Estado auth actual
### Core auth (existente)
- `POST /v1/auth/login`
- `GET /v1/auth/me`
- `GET /v1/auth/protected`
- JWT + sesion local TV + guard de navegacion.

### Device login QR (nuevo en TASK_008)
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- Estados de sesion: `pending`, `approved`, `expired`, `denied`.
- Web minima de aprobacion: `GET /auth/device?sessionId=...`.

### TV app
- Pantalla login dual activa (manual + QR en la misma pantalla).
- Polling QR con login automatico al aprobar.
- Manejo de expiracion/error con regeneracion de QR.
- Login manual se mantiene funcional.
- Hotfix compilacion aplicado: visibilidad de AuthInterceptor ajustada para compilar :libs:network.

## Dependencias externas actuales
- XUI sin cambios para TASK_008.
- OTP real y proveedor SMS siguen fuera de esta fase.

## Riesgos actuales
- Sesiones QR en memoria (sin persistencia tras reinicio backend).
- Web minima aun no migrada a web-app formal.

## Proximo enfoque recomendado
- Validar E2E real TV + movil en LAN.
- Luego endurecer persistencia/seguridad de device sessions y planificar migracion de web minima a `apps/web-app`.


