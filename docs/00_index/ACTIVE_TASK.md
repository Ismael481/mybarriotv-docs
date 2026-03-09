# ACTIVE_TASK

Tarea activa: **TASK_042_web_auth_admin_internal_partition_and_smoke_extension**

Estado actual:
- Particion interna incremental completada en auth/admin web.
- Helpers separados por dominio (`login-helpers.js`, `admin-helpers.js`) y `.app.js` como orquestadores.
- Smoke contractual extendido y estable (`npm test` 5/5).

Objetivo inmediato:
- Planificar siguiente particion fina de `login.app.js`/`admin.app.js` por dominio de eventos/render para reducir tamaño restante.

Archivos foco:
- `docs/02_tasks/TASK_042_web_auth_admin_internal_partition_and_smoke_extension.md`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `backend/test/minimum-foundation.test.js`
