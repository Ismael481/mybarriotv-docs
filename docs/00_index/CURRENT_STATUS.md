# CURRENT_STATUS

## Estado general (2026-03-12)
- `TASK_054_sincronizacion_indices_post_task_053`: completada.
- `TASK_055_xui_account_link_foundation`: completed.
- `TASK_056_auditoria_stream_cortes_audio_xui_player`: completada.
- `TASK_057_playback_failover_y_hardening_audio_stream`: completed (validada manualmente en TV fisica).
- `TASK_058_xui_admin_api_discovery_for_auto_line_provisioning`: completada.
- `TASK_059_xui_ops_provision_create_and_link`: completada.
- `TASK_060_xui_ops_provisioning_hardening`: completed.
- `TASK_061_ops_xui_link_review_and_actions`: completed.
- `BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui`: closed (mitigacion validada en TV fisica).
- Tarea activa actual: ninguna.

## Cierre TASK_060 (2026-03-12)
- Hardening minimo aplicado en provisioning `create+link` para validar bouquets antes de alta (modo `enforce|warn|off`).
- `bouquetIds` invalidos ahora se rechazan con `400` + `XUI_ADMIN_INVALID_BOUQUET_IDS` cuando la validacion esta en `enforce`.
- Caso de permisos insuficientes/API key invalida mapeado a `403` + `XUI_ADMIN_FORBIDDEN` (probado en test controlado).
- Flujo exitoso existente de provisioning se mantiene operativo.
- Validacion tecnica: `npm test` backend OK (`11` tests, `0` fallos).

## Riesgo/pendiente externo
- Rotar `XUI_ADMIN_API_KEY` en XUI y entorno backend (paso externo al repo).
- Variantes de panel XUI pueden requerir `XUI_ADMIN_BOUQUET_VALIDATION_MODE=warn` u `off` si `get_bouquets` no es fiable.

## Cierre TASK_061 (2026-03-12)
- Nuevo endpoint ops minimo: `GET /v1/auth/ops/accounts/:accountId/xui/link`.
- Reutilizacion de accion existente: `POST /v1/auth/ops/accounts/:accountId/xui/provision`.
- Evidencia minima trazable:
  - cuenta con link existente consultada como `resolved=true`;
  - cuenta sin link consultada como `resolved=false`, provisionada y reconsultada como `resolved=true`.
- Validacion tecnica: `npm test` backend OK (`12` tests, `0` fallos).
