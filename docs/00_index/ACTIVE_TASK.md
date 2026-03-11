# ACTIVE_TASK

Tarea activa unica: **TASK_055_xui_account_link_foundation**

Estado: `in_progress`

Objetivo:
- Definir e implementar la base minima para resolver identidad XUI por cuenta autenticada desde backend.
- Mantener estable el bridge `App TV -> Backend -> XUI` sin cambios de arquitectura ni playback.

Alcance:
- `backend/src/authPersistence.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_054_sincronizacion_indices_post_task_053.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Restricciones:
- No implementar billing.
- No abrir panel admin nuevo.
- No mezclar multi-servidor XUI.
- No hacer refactor amplio.
- No romper playback firmado/resuelto previamente.

Criterio de exito:
- Existe resolucion clara y documentada de identidad XUI para cuenta autenticada.
- Backend expone el contexto XUI de forma controlada (`GET /v1/auth/xui/context`).
- El bridge actual `App TV -> Backend -> XUI` se mantiene estable.

Prueba minima:
1. Ejecutar `npm test` en `backend/` y confirmar caso de `auth/xui/context` para cuenta vinculada/no vinculada.
2. Verificar que `ACTIVE_TASK`, `CURRENT_STATUS` y `CHATGPT_CONTEXT` apuntan a `TASK_055`.
