# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006: implementada.
- TASK_007: cerrada como diseno.
- TASK_008: implementada.
- TASK_009: implementada y validada.
- TASK_010: implementada.
- TASK_011: implementada en codigo; pendiente validacion E2E final de usuario.

## Auth backend vigente
- Core:
  - `POST /v1/auth/login`
  - `GET /v1/auth/me`
  - `GET /v1/auth/protected`
- Registro SMS:
  - `POST /v1/auth/register/request-otp`
  - `POST /v1/auth/register/verify-otp`
  - `POST /v1/auth/register/complete`
- Reset password SMS:
  - `POST /v1/auth/password-reset/request-otp`
  - `POST /v1/auth/password-reset/verify-otp`
  - `POST /v1/auth/password-reset/complete`
- Device login QR:
  - `POST /v1/auth/device/start`
  - `GET /v1/auth/device/status/:sessionId`
  - `POST /v1/auth/device/approve`
  - `POST /v1/auth/device/exchange`
- Device binding:
  - `GET /v1/auth/devices`
  - `POST /v1/auth/devices/revoke`

## Web auth
- Superficie unica en `/auth/login` (modos signup/reset/device-approve/profile).
- Rutas legacy (`/auth/device`, `/auth/register`) quedan por redireccion.
- Registro y reset por SMS OTP funcionales con validaciones y rate limit basico.
- Perfil web minimo activo y aprobacion TV automatica en modo `device-approve`.

## TV app auth
- Login manual + QR funcionales.
- Login screen en layout dual: QR izquierda / login derecha.
- QR sin boton manual de regeneracion; regeneracion automatica en `expired` o `denied`.
- Demo corta para cuentas registradas (`AUTH_ACCOUNT_DEMO_TTL_SECONDS`, default 60s).
- Expiracion demo: auto-logout + aviso en login (`demo expirada`).

## Riesgos abiertos
- Persistencia auth en JSON (no DB productiva).
- Falta validacion E2E final completa en entorno operativo del usuario.
