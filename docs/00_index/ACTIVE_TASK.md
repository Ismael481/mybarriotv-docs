# ACTIVE_TASK

Tarea activa: **TASK_041_web_auth_admin_js_modularization_incremental**

Estado actual:
- Modularizacion incremental del JS web auth/admin completada.
- `login.html` y `admin.html` ya no tienen logica JS inline.
- `npm test` sigue OK (`5/5`) tras extraccion a assets y helper compartido.

Objetivo inmediato:
- Definir siguiente iteracion de particion interna de `login.app.js` y `admin.app.js` en modulos mas finos por dominio.

Archivos foco:
- `docs/02_tasks/TASK_041_web_auth_admin_js_modularization_incremental.md`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/web-common.js`
