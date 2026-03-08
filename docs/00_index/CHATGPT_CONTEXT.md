# CHATGPT_CONTEXT

Fecha: 2026-03-08
Rama: `main`
Tarea activa: `TASK_018_customer_account_completion_and_self_service_hardening`

## Resumen operativo
- Perfil cliente operativo:
  - completion obligatorio solo de correo al primer acceso.
  - cambio de telefono y contrasena con OTP en bloque compacto.
  - perfiles con avatar + estado `active|inactive`.

## Cambios clave recientes
- Backend:
  - store auth `version=7` con `email` de cuenta, cooldown de desvinculacion, requests OTP de cambios de cuenta y `status` por perfil.
  - nuevo modulo `backend/src/accountChangeOtp.js`.
  - nuevos endpoints protegidos:
    - `GET/POST /v1/auth/account`
    - `POST /v1/auth/account/password/*`
    - `POST /v1/auth/account/phone/*`
    - `POST /v1/auth/profiles/:profileId`
    - `POST /v1/auth/ops/accounts/:accountId/profiles/:profileId/status`
  - `GET /v1/auth/me` y `GET /v1/auth/devices` ahora incluyen datos para completion/cooldown.
  - perfiles se fuerzan a `inactive` cuando cuenta pasa a `expired|suspended`.
- Web:
  - `/auth/login?mode=profile` ahora muestra `Cuenta del cliente`.
  - bloque de completion visible cuando falta correo en la cuenta.
  - modal de edicion de cuenta con blur de fondo para organizar cambios.
  - telefono/contrasena con OTP dentro del modal (con ver/ocultar en input de nueva contrasena).
  - perfiles de consumo con selector de avatar al crear y tarjeta con avatar + badge de estado.
  - cada perfil muestra boton lapiz para editar nombre y avatar.
  - cada perfil muestra una linea de referencia con nombre de TV vinculada.
  - correo en meta-card con tipografia compacta + ellipsis para evitar overflow.
  - `profileCompletionRequired` backend ahora depende solo de correo.
  - `/admin` muestra perfiles por cuenta y permite activar/desactivar cada perfil.

## Pruebas tecnicas ejecutadas
- `node --check` OK:
  - `backend/src/authPersistence.js`
  - `backend/src/accountChangeOtp.js`
  - `backend/src/server.js`
- Smoke de integracion en backend temporal:
  - completion pasa de `profileCompletionRequired=true` a `false`.
  - cambio de username en `/v1/auth/account` OK.
  - cooldown de desvinculacion responde `429 DEVICE_REVOKE_COOLDOWN` en segunda accion.
  - validaciones OTP de cuenta devuelven codigos esperados (`PASSWORD_WEAK`, `PHONE_UNCHANGED`).

## Cambios manuales externos
- Ninguno en XUI.
- Para pruebas OTP reales se requiere `SMS_ZDSMS_*` configurado en backend.
- Reiniciar backend tras desplegar para que queden activos endpoints `/v1/auth/account/*`.
- Reiniciar backend tras desplegar para activar tambien `POST /v1/auth/profiles/:profileId`.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_018_customer_account_completion_and_self_service_hardening.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
