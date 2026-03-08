# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006 a TASK_013: implementadas (TASK_013 validada).
- TASK_014: implementada y validada.
- TASK_015: validada (dashboard admin minimo base).
- TASK_016: implementada en codigo; pendiente validacion visual/manual final.
- TASK_017: implementada en codigo; pendiente validacion visual/manual final.
- TASK_018: implementada en codigo; pendiente validacion visual/manual final.

## Auth backend vigente
- Core:
  - `POST /v1/auth/login`
  - `GET /v1/auth/me`
  - `GET /v1/auth/account`
  - `POST /v1/auth/account`
  - `POST /v1/auth/account/password/request-otp`
  - `POST /v1/auth/account/password/verify-otp`
  - `POST /v1/auth/account/password/complete`
  - `POST /v1/auth/account/phone/request-otp`
  - `POST /v1/auth/account/phone/verify-otp`
  - `POST /v1/auth/account/phone/complete`
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
- Account profiles (web):
  - `GET /v1/auth/profiles`
  - `POST /v1/auth/profiles`
  - `POST /v1/auth/profiles/:profileId`
- Device binding:
  - `GET /v1/auth/devices`
  - `POST /v1/auth/devices/revoke`
- Ops minima:
  - `GET /v1/auth/ops/accounts`
  - `GET /v1/auth/ops/accounts/:accountId`
  - `POST /v1/auth/ops/accounts/:accountId/status`
  - `POST /v1/auth/ops/accounts/:accountId/devices/:deviceKey/status`
  - `POST /v1/auth/ops/accounts/:accountId/profiles/:profileId/status`

## Modelo minimo de roles (TASK_014)
- Rol persistente de cuenta: `customer|operator`.
- `GET /v1/auth/me` devuelve role para decidir superficie web.
- `/v1/auth/ops/*` requiere `role=operator` como mecanismo principal.
- `AUTH_OPS_ALLOWED_SUBS` queda como compatibilidad temporal/bootstrap.
- `AUTH_BOOTSTRAP_OPERATOR_USERNAMES` permite promover cuentas operadoras al boot.

## Web auth/profile
- Superficie principal en `/auth/login` (signin/signup/reset/profile).
- Flujo QR web separado en endpoint dedicado: `/auth/device-approve`.
- `POST /v1/auth/device/start` genera `qrUrl` directo a `/auth/device-approve?sessionId=...`.
- Perfil cliente mejorado con perfiles de consumo (max 2) por cuenta.
- Perfil cliente en `/auth/login?mode=profile` renovado a layout card (visual moderno) manteniendo la misma logica.
- TASK_018: profile cliente exige completion minimo de cuenta con correo al primer acceso.
- TASK_018: perfil cliente operativo:
  - resumen de cuenta (usuario, correo, telefono, contrasena enmascarada).
  - edicion via modal (blur) para cuenta y seguridad.
  - cambio de telefono y contrasena con OTP dentro del modal.
  - perfiles de consumo con avatar y estado `active|inactive`.
  - edicion de perfil (lapiz) para nombre + avatar.
  - bajo cada perfil se muestra nombre de TV vinculada (vista cliente, resumen).
- TASK_018: admin `/admin` incluye gestion de perfiles por cuenta:
  - visualiza perfiles en detalle de cuenta.
  - cambia `profileStatus` (`active|inactive`) por perfil.
- TASK_018: al cambiar cuenta a `expired`/`suspended`, los perfiles de la cuenta pasan a `inactive`.
- Dashboard admin minimo dedicado en `/admin` (alias `/ops`) para operadores.
- Perfil mantiene funciones base y expone acceso a admin solo para `role=operator`.
- `customer` queda fuera de dashboard admin.
- TASK_016: dashboard admin mejorado con resumen por estado, filtros, detalle ampliado y mejor feedback UX.
- TASK_016 UI refresh: estilo visual modernizado (layout/cards/jerarquia visual) sin cambios de logica ops.
- TASK_016 UI refresh (pass 3): layout alineado a referencia del usuario con sidebar oscuro + contenido claro.
- TASK_016 UI refresh (pass 4): pulido visual premium (tipografia/espaciado/contraste) manteniendo contratos y seguridad.
- Estilo visual actual preservado (`v34` + `v34-custom`).

## Riesgos abiertos
- Persistencia auth en JSON (sin DB productiva).
- Sin RBAC completo (solo rol minimo).
- Validacion visual/manual final pendiente de mejoras TASK_016 en dashboard `/admin` (operator/customer).
- Validacion visual/manual final pendiente de TASK_017 (UX perfil cliente + flujo QR approve).
- Validacion manual pendiente de TASK_018 en UX final (OTP telefono/contrasena + estados de perfil desde admin).
