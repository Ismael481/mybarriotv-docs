# CHATGPT_CONTEXT

Fecha: 2026-03-12
Rama: `main`
Tarea activa: `ninguna`

## Resumen operativo
- `TASK_057`: completed (validacion manual en TV fisica confirmada).
- `BUG_022`: closed.
- `TASK_060`: completed con hardening minimo de provisioning XUI.

## Estado actual
- No hay tarea activa abierta.
- Provisioning `create+link` mantiene el caso exitoso y suma defensa minima contra bouquets invalidos.
- Caso de permisos insuficientes/API key invalida queda mapeado explicitamente a `XUI_ADMIN_FORBIDDEN`.

## Cambios manuales externos requeridos
- Rotar `XUI_ADMIN_API_KEY` (paso externo a este repo).
- Confirmar en panel real la politica de permisos del Access Code/API key tras la rotacion.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_060_xui_ops_provisioning_hardening.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`