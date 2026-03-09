# ACTIVE_TASK

Tarea activa: **TASK_042_web_auth_admin_internal_partition_and_smoke_extension**

Estado actual:
- Particion interna incremental completada en auth/admin web.
- `login.app.js` usa `login-state.js` y `login-render.js`.
- `admin.app.js` usa `admin-api.js` y `admin-render.js`.
- Smoke contractual extendido y estable (`npm test` 5/5).

Objetivo inmediato:
- Cerrar TASK_042 y dejar base lista para una siguiente particion fina por handlers/eventos.

Archivos foco:
- `docs/02_tasks/TASK_042_web_auth_admin_internal_partition_and_smoke_extension.md`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `backend/test/minimum-foundation.test.js`
