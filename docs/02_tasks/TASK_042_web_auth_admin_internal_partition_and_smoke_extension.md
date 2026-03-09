# TASK_042_web_auth_admin_internal_partition_and_smoke_extension

Estado: completed

Fecha de creacion:
2026-03-09

Ultima actualizacion:
2026-03-09

Objetivo:
Continuar modularizacion incremental del frontend web particionando internamente `login.app.js` y `admin.app.js`, y ampliar smoke contractual ligero sin cambiar producto ni contratos backend.

Alcance:
- Particion interna por responsabilidades en auth/admin.
- Mantener UX, reglas de negocio y contratos backend existentes.
- Extender smoke para assets modularizados y rutas ops principales.

Archivos tocados:
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/login-helpers.js`
- `apps/web-app/public/auth/assets/login-state.js`
- `apps/web-app/public/auth/assets/login-render.js`
- `apps/web-app/public/auth/assets/admin-helpers.js`
- `apps/web-app/public/auth/assets/admin-api.js`
- `apps/web-app/public/auth/assets/admin-render.js`
- `backend/test/minimum-foundation.test.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_042_web_auth_admin_internal_partition_and_smoke_extension.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Web auth:
  - `login-state.js` nuevo con estado OTP/persistencia (`get/setOtpRequestId`, `get/setOtpExpiryMs`, `hasActiveOtpRequest`, etc.).
  - `login-render.js` nuevo con render/error mapping para perfil y ops-lite.
  - `login.app.js` actualizado para orquestar flujo/eventos consumiendo modulos `state` + `render`.
- Web admin:
  - `admin-api.js` nuevo con wrapper de rutas ops usadas por dashboard.
  - `admin-render.js` nuevo con filtros, render de cuentas/dispositivos/perfiles/auditoria y mensajes de error.
  - `admin.app.js` actualizado para orquestar eventos/estado usando modulos `api` + `render`.
- HTML limpio:
  - `login.html` y `admin.html` mantienen markup + carga de assets modularizados.
- Smoke extendido en backend test:
  - valida carga de nuevos assets (`login-state.js`, `login-render.js`, `admin-api.js`, `admin-render.js`)
  - valida contratos base de login/admin (`/v1/auth/me`, `/v1/auth/devices`, `/v1/auth/ops/accounts`, `/v1/auth/ops/accounts/:id`)
  - valida rutas ops principales usadas por admin (`status`, `expiry`, `device status`, `profile status`)

Contratos preservados:
- Login/register/device-approve/auth profile/admin ops sin cambios funcionales.
- Endpoints backend auth/ops sin cambios de contrato.

Pruebas ejecutadas:
- `npm test` en `backend/`.
- Resultado: `5` pruebas OK, `0` fallos.

Riesgos:
- `login.app.js` y `admin.app.js` reducen acoplamiento, pero aun conservan tamano considerable para futuras iteraciones.

Pendiente de prueba:
- Validacion manual en navegador real de caminos completos UI (login, OTP, perfil, admin).

Resultado esperado alcanzado:
- Particion interna clara por responsabilidad (`state/render/api`) en web auth/admin.
- HTML limpio y enfocado en markup.
- Smoke/contratos siguen pasando.

Cambios manuales externos:
- Ninguno en XUI.
- Ninguna configuracion externa requerida.
