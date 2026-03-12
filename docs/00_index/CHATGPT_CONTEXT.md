# CHATGPT_CONTEXT

Fecha: 2026-03-11
Rama: `main`
Tarea activa: `TASK_055_xui_account_link_foundation`

## Resumen operativo
- Estado funcional confirmado:
  - `TASK_055`: backend ya resuelve `GET /v1/auth/xui/context` por `xuiLink` manual.
  - `TASK_057`: mitigacion playback (failover/hardening) completada y validada manualmente por el usuario en TV fisica.
  - `BUG_022`: cerrado con mitigacion validada manualmente en TV real.
- Discovery (`TASK_058`) validado en panel real:
  - access code operativo `lHpqPGtQ`.
  - `user_info/get_bouquets/create_line` funcionando.
- `TASK_059` completada:
  - endpoint ops nuevo para provisioning `create+link`.
  - persistencia de `xuiLink` sin edicion manual del store.
  - test automatizado de idempotencia en verde.
  - validacion manual real: cuenta `Isma` resolviendo `lineId=5` por `GET /v1/auth/xui/context`.

## Archivos clave recientes
- `backend/src/xuiAdminClient.js`
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/test/minimum-foundation.test.js`
- `backend/.env.example`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_059_xui_ops_provision_create_and_link.md`
- `docs/02_tasks/TASK_058_xui_admin_api_discovery_for_auto_line_provisioning.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/02_tasks/TASK_057_playback_failover_y_hardening_audio_stream.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion minima esperada
- `npm test` en `backend/` en verde (incluye test `ops xui provision creates and links line idempotently`).
- `TASK_059` ya validada manualmente en runtime real.
- `TASK_057` cerrada tras validacion manual satisfactoria en TV fisica.
- `BUG_022` cerrado con la evidencia manual actual.

## Cambios manuales externos requeridos
- Configurar en backend:
  - `XUI_ADMIN_BASE_URL` (ej: `http://panel.mybarriotv.com/lHpqPGtQ/`).
  - `XUI_ADMIN_API_KEY` (API Password del usuario API).
- Mantener Access Code habilitado y grupos correctos.
- Si se requiere paquete/bouquet especifico y el panel usa parametro distinto, enviarlo via `xuiCreateParams`.
- Rotar `XUI_ADMIN_API_KEY` por exposicion en consola/chat de pruebas.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_059_xui_ops_provision_create_and_link.md`
- `docs/02_tasks/TASK_058_xui_admin_api_discovery_for_auto_line_provisioning.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/02_tasks/TASK_057_playback_failover_y_hardening_audio_stream.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
