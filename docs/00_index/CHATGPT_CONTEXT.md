# CHATGPT_CONTEXT

Fecha: 2026-03-11
Rama: `main`
Tarea activa: `TASK_055_xui_account_link_foundation`

## Resumen operativo
- `TASK_054` se cierra como sincronizacion documental validada.
- `TASK_055` abre base minima de vinculacion cuenta interna -> identidad XUI.
- Backend ahora resuelve contexto XUI por cuenta autenticada via `GET /v1/auth/xui/context`.
- Fuente de verdad del vinculo: `accountsById.<accountId>.xuiLink` en `AUTH_STORE_FILE`.

## Archivos clave de la tarea activa
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion minima
- `npm test` en `backend/` con caso de `auth/xui/context` (cuenta vinculada/no vinculada).
- Consistencia de indices con unica tarea activa en `TASK_055`.

## Cambios manuales externos requeridos
- No hay cambios manuales obligatorios en XUI para esta fundacion.
- Para validacion con linea real, se necesita identificar `lineId` existente en XUI y cargarlo en `xuiLink` del store backend.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/02_tasks/TASK_054_sincronizacion_indices_post_task_053.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
