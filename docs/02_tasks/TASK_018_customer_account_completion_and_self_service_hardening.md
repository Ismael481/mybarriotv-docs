# TASK_018_customer_account_completion_and_self_service_hardening

Estado: done (pendiente validacion visual/manual final)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Agregar una capa minima de completion y operacion de cuenta/perfiles en `/auth/login?mode=profile` y `/admin`: pedir correo al primer acceso, gestionar telefono/contrasena con OTP y habilitar estados `active|inactive` por perfil.

Alcance:
- Backend:
  - nuevo modulo OTP para cambios autenticados de cuenta (`password` y `phone`).
  - nuevos endpoints protegidos de cuenta:
    - `GET /v1/auth/account`
    - `POST /v1/auth/account`
    - `POST /v1/auth/account/password/request-otp`
    - `POST /v1/auth/account/password/verify-otp`
    - `POST /v1/auth/account/password/complete`
    - `POST /v1/auth/account/phone/request-otp`
    - `POST /v1/auth/account/phone/verify-otp`
    - `POST /v1/auth/account/phone/complete`
  - endpoint adicional para editar perfil de consumo:
    - `POST /v1/auth/profiles/:profileId`
  - `GET /v1/auth/me` y `GET /v1/auth/devices` exponen datos para UX de cuenta (completion + politica de desvinculacion).
  - cooldown de desvinculacion self-service: 1 TV cada 90 dias por cuenta.
  - persistencia JSON extendida para:
    - `email` de cuenta
    - timestamp de ultima desvinculacion self-service
    - requests OTP de cambios de cuenta
- Web profile:
  - titulo actualizado a `Cuenta del cliente`.
  - bloque obligatorio de completion cuando falta correo.
  - modal de edicion con blur para cambiar username/correo/telefono/contrasena.
  - telefono/contrasena siguen flujo OTP (request/verify/complete).
  - perfiles de consumo con avatar y estado (`active|inactive`).
  - boton lapiz por perfil para editar nombre y avatar.
  - nombre de TV vinculada visible bajo cada perfil.
- Web admin (`/admin`):
  - detalle de cuenta muestra perfiles.
  - accion para activar/desactivar perfil.
- Backend ops:
  - endpoint de estado por perfil:
    - `POST /v1/auth/ops/accounts/:accountId/profiles/:profileId/status`
  - cambio de cuenta a `expired|suspended` inactiva perfiles de la cuenta.

No tocar:
- Billing/pagos.
- Migracion a DB.
- RBAC complejo.
- TV app.

Archivos involucrados:
- `backend/src/authPersistence.js`
- `backend/src/accountChangeOtp.js`
- `backend/src/server.js`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `docs/02_tasks/TASK_018_customer_account_completion_and_self_service_hardening.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se extendio store auth a version `7` y se agrego soporte persistente para requests OTP de cambios de cuenta.
- Se agrego modulo `accountChangeOtp` con validacion OTP y max intentos para cambios de:
  - contrasena (OTP a telefono actual).
  - telefono (OTP al nuevo telefono).
- Se agrego actualizacion de identidad de cuenta (`username`, `email`, `phone`) con control de unicidad.
- Se agrego evaluacion de completion de cuenta:
  - requerido cuando `email` esta vacio.
- Se agrego estado por perfil (`active|inactive`) en persistencia de cuenta.
- Se agrego operacion ops para cambiar estado de perfil por cuenta.
- Se agrego inactivacion automatica de perfiles cuando `accountStatus` pasa a `expired|suspended`.
- Se agrego politica de desvinculacion self-service:
  - maximo una desvinculacion cada 90 dias por cuenta.
- Se actualizo profile web con:
  - bloque de completion de correo.
  - modal unico (blur) para organizar edicion de cuenta y seguridad.
  - cambio de telefono/contrasena con OTP dentro del modal.
  - perfiles de consumo con selector de avatar y badge de estado.
  - edicion de perfiles via lapiz (nombre + avatar).
  - tarjeta de perfiles mostrando avatar y linea con nombre de TV vinculada.
  - correo en tarjeta superior con estilo compacto para evitar desborde visual.
- Se actualizo admin web con listado de perfiles en detalle y accion de cambio `active|inactive`.
- Se agrego endpoint de update de perfil para UI cliente (`POST /v1/auth/profiles/:profileId`).

Pruebas tecnicas ejecutadas:
- `node --check` OK:
  - `backend/src/authPersistence.js`
  - `backend/src/accountChangeOtp.js`
  - `backend/src/server.js`
- Smoke test de integracion en backend temporal (`PORT=8095`, store JSON aislado):
  - login account OK.
  - `profileCompletionRequired=true` inicialmente.
  - completion de cuenta via `POST /v1/auth/account` OK.
  - `profileCompletionRequired=false` despues de completion.
  - cambio de username via `POST /v1/auth/account` OK.
  - primera desvinculacion TV OK; segunda devuelve `429 DEVICE_REVOKE_COOLDOWN`.
  - password change request con contrasena debil devuelve `PASSWORD_WEAK`.
  - phone change request con mismo telefono devuelve `PHONE_UNCHANGED`.

Pendiente manual de validacion:
- Reiniciar proceso backend en entorno donde se pruebe (si estaba levantado antes de estos cambios).
- Validar UX final completa en `/auth/login?mode=profile` con cuenta real:
  - completion obligatorio de correo al primer acceso.
  - cambio de telefono con OTP.
  - cambio de contrasena con OTP.
  - creacion/listado de perfiles con avatar + estado.
- Validar en `/admin`:
  - operador cambia `profileStatus` por perfil.
  - cuenta en `expired|suspended` refleja perfiles inactivos.

Cambios manuales externos:
- Ninguno en XUI.
- Para validar OTP real se requiere configuracion operativa de `SMS_ZDSMS_*` en entorno backend.
