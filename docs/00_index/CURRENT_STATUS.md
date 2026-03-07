# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006 a TASK_011: implementadas.
- TASK_012: implementada en codigo; pendiente validacion final en TV fisica.

## Auth backend vigente
- Core:
  - `POST /v1/auth/login`
  - `GET /v1/auth/me`
  - `GET /v1/auth/access`
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

## Gate de acceso comercial (TASK_012)
- Modelo de cuenta extendido: `accountStatus = trial|active|expired|suspended`.
- Modelo de dispositivo extendido: `accessStatus = allowed|blocked` (compat con `active|revoked`).
- Endpoint `GET /v1/auth/access` devuelve:
  - `accountStatus`
  - `deviceStatus`
  - `canAccessApp`
  - `reasonCode` cuando aplica (`NO_SUBSCRIPTION|ACCOUNT_EXPIRED|ACCOUNT_SUSPENDED|DEVICE_BLOCKED`)
- Login manual y QR mantienen autenticacion valida, pero el acceso final se decide en gate.

## TV app auth
- Login manual + QR funcionales.
- Gate obligatorio post-login/manual y post-exchange/QR antes de persistir sesion.
- Nuevo estado de sesion `AccessBlocked` con pantalla dedicada y mensaje por `reasonCode`.
- Navegacion controlada por `AuthState`: `LoggedOut`, `AccessBlocked`, `LoggedIn`.

## Riesgos abiertos
- Persistencia auth en JSON (sin DB productiva).
- Validacion manual pendiente en TV fisica/LAN para copy/UX final de bloqueo.
- Entorno local actual no permite compilar TV (`jvm.cfg` faltante en JBR local).
