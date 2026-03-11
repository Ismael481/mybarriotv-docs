# TASK_017_profile_surface_slots_and_qr_approve_split

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-07

Ultima actualizacion:
2026-03-08

Objetivo:
Mejorar la superficie de perfil web para clientes con perfiles de consumo (max 2 por cuenta), separar el flujo web de aprobacion QR en endpoint dedicado y evitar countdown de demo en el perfil web.

Alcance:
- Web auth/profile:
  - perfil visual mejorado.
  - soporte para crear hasta 2 perfiles de consumo por cuenta.
  - avatares de perfil alineados con assets de TV app (`boy`, `girl`, `old`).
  - countdown demo removido del perfil web (mensaje informativo para TV).
- Backend:
  - nuevo endpoint protegido `GET /v1/auth/profiles`.
  - nuevo endpoint protegido `POST /v1/auth/profiles` (max 2 perfiles).
  - `/v1/auth/me` ahora incluye perfiles de cuenta cuando aplica.
  - nueva ruta web dedicada `GET /auth/device-approve`.
  - `/auth/device` redirige a `/auth/device-approve?sessionId=...`.
- Persistencia JSON:
  - cuenta ahora soporta `profiles` en store auth.
  - auditoria de creacion de perfil (`account_profile_created`).

No tocar:
- Billing/pagos.
- Migracion a DB.
- RBAC complejo.
- TV app (sin cambios funcionales).

Archivos involucrados:
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/src/deviceLogin.js`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/device-approve.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `apps/web-app/public/auth/assets/img/profiles/boy.png`
- `apps/web-app/public/auth/assets/img/profiles/girl.png`
- `apps/web-app/public/auth/assets/img/profiles/old.png`
- `docs/02_tasks/TASK_017_profile_surface_slots_and_qr_approve_split.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se agrego modelo minimo de perfiles de consumo por cuenta (maximo 2).
- Se agregaron endpoints web/backend para listar y crear perfiles por cuenta autenticada.
- Se mejoro perfil web para mostrar perfiles y crear nuevos con avatar.
- Se rediseno visual de `mode=profile` en formato card (referencia UI: `1-html-css-card-profile-card`) adaptado a paleta azul MyBarrioTV.
- Se copiaron avatares desde TV app a assets web.
- Se separo flujo QR approve en endpoint/ruta propia (`/auth/device-approve`).
- Se ajusto `qrUrl` de `POST /v1/auth/device/start` para apuntar directo a `/auth/device-approve`.
- Se removio countdown de demo en profile web; queda mensaje de alcance TV.

Pruebas tecnicas ejecutadas:
- `node --check` OK:
  - `backend/src/server.js`
  - `backend/src/authPersistence.js`
- Smoke tests API/web:
  - `/auth/device?sessionId=...` redirige a `/auth/device-approve?sessionId=...`.
  - `/auth/device-approve` responde 200.
  - `/auth/login?mode=profile` contiene `profile-card-shell` y `profile-hero-avatar` (nuevo layout card).
  - `createDeviceLoginSession()` genera `qrUrl` con `/auth/device-approve?sessionId=...` (requiere restart de backend para verse en proceso ya levantado).
  - `GET /v1/auth/profiles` responde perfiles para cuenta.
  - `POST /v1/auth/profiles` crea perfiles hasta maximo 2.
  - tercer intento devuelve `ACCOUNT_PROFILE_LIMIT`.
  - `/auth/login?mode=profile` contiene nueva seccion de perfiles.

Pendiente manual de validacion:
- Validar UX final de perfil cliente (tarjetas, creacion de perfil, avatares).
- Validar flujo QR approve completo con TV real usando `/auth/device-approve`.
- Confirmar expectativa de negocio sobre demo 1 sola vez en TV (politica backend de demo).


