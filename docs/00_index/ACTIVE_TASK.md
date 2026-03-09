# ACTIVE_TASK

Tarea activa: **TASK_040_backend_auth_ops_modularization_incremental**

Estado actual:
- Modularizacion incremental auth/ops completada sin cambio de contratos funcionales.
- `server.js` ahora delega rutas auth/ops/web y servicios de acceso/autorizacion.
- Suite minima (`npm test`) sigue pasando 5/5 tras la extraccion.

Objetivo inmediato:
- Definir siguiente iteracion de modularizacion backend/web con pasos pequenos y cobertura incremental.

Archivos foco:
- `docs/02_tasks/TASK_040_backend_auth_ops_modularization_incremental.md`
- `backend/src/routes/authOpsRoutes.js`
- `backend/src/services/accessDecision.js`
- `backend/src/services/opsAuthorization.js`
