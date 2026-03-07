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

### Device login QR (TASK_008)
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- Estados de sesion: `pending`, `approved`, `expired`, `denied`.
- Web minima de aprobacion: `GET /auth/device?sessionId=...`.

### Vinculacion basica de dispositivo (extension TASK_008)
- TV envia headers de identidad de dispositivo en login manual y QR exchange.
- Backend registra dispositivo por usuario autenticado en memoria.
- Endpoints de consulta:
  - `GET /v1/auth/me` (incluye `devices`)
  - `GET /v1/auth/devices` (protegido)
- MAC se usa cuando Android la expone; se agrega serial, Widevine ID, fingerprint y fallback a identificador estable del dispositivo.

### TV app
- Pantalla login dual activa (manual + QR en la misma pantalla).
- Polling QR con login automatico al aprobar.
- Manejo de expiracion/error con regeneracion de QR.
- Login manual se mantiene funcional.

## Dependencias externas actuales
- XUI sin cambios para TASK_008.
- OTP real y proveedor SMS siguen fuera de esta fase.

## Riesgos actuales
- Sesiones QR y dispositivos vinculados en memoria (sin persistencia tras reinicio backend).
- Web minima aun no migrada a web-app formal.

## Proximo enfoque recomendado
- Validar E2E real TV + movil en LAN.
- Persistir vinculacion de dispositivo en DB y definir reglas de revocacion/limites.
- Planificar migracion de web minima a `apps/web-app`.



