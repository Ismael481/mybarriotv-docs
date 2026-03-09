# CHATGPT_CONTEXT

Fecha: 2026-03-09
Rama: `main`
Tarea activa: `TASK_039_minimum_automated_test_foundation`

## Resumen operativo
- TASK_039 completada con base minima de pruebas automatizadas en backend.
- Suite actual valida flujos criticos de auth/access/ops y smoke web auth/admin sin depender de Android local.
- No hubo cambios de arquitectura, DB, billing/pagos ni RBAC completo.

## Comando de pruebas
- En `backend/`: `npm test`

## Cobertura minima confirmada
- Backend falla si `AUTH_JWT_SECRET` es inseguro.
- Backend arranca con secreto valido.
- `GET /v1/auth/access` para `active|expired|suspended`.
- Bloqueo contractual por `canAccessApp=false`.
- Preflight `DELETE` en ruta ops.
- Smoke web de `/auth/login`, `/admin`, `/auth/device-approve` y assets.
- Contrato JSON base de `/v1/auth/me` y `/v1/auth/ops/accounts`.

## Cambios manuales externos
- Ninguno en XUI.
- Ninguna configuracion externa requerida para esta tarea.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_039_minimum_automated_test_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
