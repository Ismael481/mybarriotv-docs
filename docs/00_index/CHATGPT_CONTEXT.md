# CHATGPT_CONTEXT

Fecha: 2026-03-12
Rama: `main`
Tarea activa: `ninguna`

## Resumen operativo
- `TASK_064`: completed con fundacion DB operativa minima (SQLite nativo).
- Se mantiene compatibilidad de flujo actual en convivencia temporal JSON + DB.
- `TASK_063`, `TASK_062`, `TASK_061`: completed.

## Estado actual
- Backend ya persiste entidades operativas criticas en `AUTH_OPERATIONAL_DB_FILE`:
  - cuentas operativas y estado,
  - vinculo XUI por cuenta,
  - dispositivos vinculados,
  - eventos operativos.
- `GET /v1/auth/xui/context` y review ops de link mantienen comportamiento actual sin ruptura (DB preferente + fallback JSON).
- No hay tarea activa abierta.

## Cambios manuales externos requeridos
- Rotar `XUI_ADMIN_API_KEY` (externo al repo).
- Confirmar en panel real permisos del Access Code/API key en XUI tras la rotacion.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_064_operational_database_foundation.md`
- `docs/04_decisions/ADR_004_operational_db_sqlite_foundation.md`
- `docs/02_tasks/TASK_063_web_internal_xui_autolink_review_surface.md`
- `docs/02_tasks/TASK_062_auto_xui_link_on_account_activation.md`
- `docs/02_tasks/TASK_061_ops_xui_link_review_and_actions.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
