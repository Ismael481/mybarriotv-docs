# CHATGPT_CONTEXT

Fecha: 2026-03-12
Rama: `main`
Tarea activa: `TASK_060_xui_ops_provisioning_hardening` (`in_progress`)

## Resumen operativo
- Cierres confirmados por validacion manual del usuario en TV fisica:
  - `TASK_057`: completed (failover/audio/reproduccion estables).
  - `BUG_022`: closed.
- Base de provisioning XUI ya implementada y cerrada en `TASK_059` (flujo `create+link`).

## Estado actual
- Una unica tarea activa abierta: `TASK_060_xui_ops_provisioning_hardening`.
- Alcance actual de `TASK_060`: hardening minimo y trazable del provisioning existente.
- Sin cambios de arquitectura general; sin billing; sin multi-servidor.
- Validacion runtime ya ejecutada en provisioning: exito `create+link` y error controlado mapeado (`XUI_ADMIN_ACTION_FAILED`).
- Gap detectado: falta validacion minima de bouquets reales para evitar altas con `bouquetIds` no esperado.

## Cambios manuales externos requeridos
- Rotar `XUI_ADMIN_API_KEY` por exposicion previa en pruebas.
- Verificar permisos de Access Code/API key para `create_line`, `get_bouquets`, `get_line/get_lines`.
- Validar en panel XUI los nombres exactos de parametros de bouquets antes de ajustes puntuales.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_059_xui_ops_provision_create_and_link.md`
- `docs/02_tasks/TASK_060_xui_ops_provisioning_hardening.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
