# ACTIVE_TASK

Tarea activa: **TASK_044_web_login_handlers_and_admin_actions_partition**

Estado actual:
- Particion fina completada en web auth/admin.
- Hotfix aplicado de codificacion UI en perfiles web (caracteres mojibake corregidos).
- `login.app.js` reducido y centrado en wiring/orquestacion.
- `admin.app.js` reducido delegando acciones operativas a `admin-actions.js`.
- `npm test` backend en verde (5/5).

Objetivo inmediato:
- Mantener estabilidad de modularizacion incremental sin alterar contratos backend.

Archivos foco:
- `docs/02_tasks/TASK_044_web_login_handlers_and_admin_actions_partition.md`
- `apps/web-app/public/auth/assets/login-render.js`
- `apps/web-app/public/auth/assets/login-handlers.js`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/admin-actions.js`
- `backend/test/minimum-foundation.test.js`
