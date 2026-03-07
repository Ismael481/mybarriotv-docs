# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006 a TASK_013: implementadas (TASK_013 validada).
- TASK_014: implementada y validada.
- TASK_015: implementada en codigo; pendiente validacion manual final.

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
- Ops minima:
  - `GET /v1/auth/ops/accounts`
  - `GET /v1/auth/ops/accounts/:accountId`
  - `POST /v1/auth/ops/accounts/:accountId/status`
  - `POST /v1/auth/ops/accounts/:accountId/devices/:deviceKey/status`

## Modelo minimo de roles (TASK_014)
- Rol persistente de cuenta: `customer|operator`.
- `GET /v1/auth/me` devuelve role para decidir superficie web.
- `/v1/auth/ops/*` requiere `role=operator` como mecanismo principal.
- `AUTH_OPS_ALLOWED_SUBS` queda como compatibilidad temporal/bootstrap.
- `AUTH_BOOTSTRAP_OPERATOR_USERNAMES` permite promover cuentas operadoras al boot.

## Web auth/profile
- Superficie unificada en `/auth/login` (signin/signup/reset/device-approve/profile).
- Dashboard admin minimo dedicado en `/admin` (alias `/ops`) para operadores.
- Perfil mantiene funciones base y expone acceso a admin solo para `role=operator`.
- `customer` queda fuera de dashboard admin.
- Estilo visual actual preservado (`v34` + `v34-custom`).

## Riesgos abiertos
- Persistencia auth en JSON (sin DB productiva).
- Sin RBAC completo (solo rol minimo).
- Validacion visual/manual final pendiente de dashboard `/admin` en navegador (operator/customer).
