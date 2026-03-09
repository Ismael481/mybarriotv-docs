# TASK_041_web_auth_admin_js_modularization_incremental

Estado: completed

Fecha de creacion:
2026-03-09

Ultima actualizacion:
2026-03-09

Objetivo:
Reducir riesgo de mantenibilidad del frontend web moviendo JS inline de auth/admin a archivos mantenibles, sin cambiar contratos ni reglas de negocio.

Alcance:
- Extraccion incremental del JS inline de `login.html` y `admin.html`.
- Introduccion de helper compartido para cliente API/utilidades.
- Mantener UX y contratos funcionales existentes.
- Validacion por `npm test` y smoke contractual.

Archivos tocados:
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/web-common.js`
- `backend/test/minimum-foundation.test.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_041_web_auth_admin_js_modularization_incremental.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se removio JS inline de `login.html` y `admin.html`.
- Se movio logica a assets externos:
  - `auth/assets/login.app.js`
  - `auth/assets/admin.app.js`
- Se agrego utilitario compartido `auth/assets/web-common.js` con:
  - `createApiClient` (api/client helper)
  - `setLine` (utilidad UI compartida)
- `login.app.js` y `admin.app.js` consumen helper compartido para API y utilidades de UI.
- Se mantuvo markup y UX actual (sin rediseño ni nuevas pantallas).

Contratos preservados:
- Auth/login/register/device approve.
- Admin cuentas/dispositivos/perfiles.
- Endpoints backend auth/ops sin cambios.

Pruebas ejecutadas:
- `npm test` en `backend/`.
- Resultado: `5` pruebas OK, `0` fallos.
- Smoke extendido para verificar carga de assets modularizados:
  - `/auth/assets/web-common.js`
  - `/auth/assets/login.app.js`
  - `/auth/assets/admin.app.js`
  - referencias en `/auth/login` y `/admin`

Riesgos:
- Aunque se elimino JS inline, los archivos `login.app.js` y `admin.app.js` aun son extensos y requieren iteraciones futuras de particion por dominio.

Pendiente de prueba:
- Validacion manual UI end-to-end en navegador real para todos los caminos de perfil/admin.

Resultado esperado alcanzado:
- `login.html` y `admin.html` quedan mas livianos y enfocados en markup.
- JS principal extraido a archivos mantenibles con helper compartido.
- `npm test` sigue pasando sin regresiones detectadas.

Cambios manuales externos:
- Ninguno en XUI.
- Ninguna configuracion externa requerida.
