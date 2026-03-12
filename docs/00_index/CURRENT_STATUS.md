# CURRENT_STATUS

## Estado general (2026-03-11)
- `TASK_054_sincronizacion_indices_post_task_053`: completada.
- `TASK_055_xui_account_link_foundation`: in_progress, con validacion minima ya lograda (`/v1/auth/xui/context` resuelve `true` cuando existe `xuiLink`).
- `TASK_056_auditoria_stream_cortes_audio_xui_player`: completada.
- `TASK_057_playback_failover_y_hardening_audio_stream`: in_progress, mitigacion implementada y pendiente validacion manual en TV fisica.
- `TASK_058_xui_admin_api_discovery_for_auto_line_provisioning`: completada.
- `TASK_059_xui_ops_provision_create_and_link`: completada.
- Tarea activa actual: `TASK_057_playback_failover_y_hardening_audio_stream`.

## Avance de `TASK_059` (implementacion)
- Backend agrega cliente Admin API XUI (`xuiAdminClient`) con manejo de timeout, redirects/login y parse JSON estricto.
- Backend agrega persistencia tipada para `xuiLink` (`updateAccountXuiLink`) en account store.
- Endpoint nuevo:
  - `POST /v1/auth/ops/accounts/:accountId/xui/provision`
  - crea linea en XUI (`create_line`) y enlaza cuenta local en una sola operacion.
  - idempotencia basica: si ya hay `xuiLink`, responde `alreadyLinked=true` sin reprovision (salvo `forceProvision=true`).
- Evidencia runtime real en panel del usuario:
  - Access Code operativo: `lHpqPGtQ`.
  - `user_info` y `get_bouquets` OK.
  - `create_line` OK (`STATUS_SUCCESS`, linea creada con id real).
  - provisioning forzado desde backend OK: cuenta `Isma` enlazada a `lineId=5` (`xuiUsername=isma_auto_3507`).
- Pruebas automatizadas backend:
  - `npm test` OK (`9` tests, `0` fallos), incluyendo test nuevo de provisioning/idempotencia.
- Estado: implementacion + validacion manual minima completadas.

## Pendientes de prueba manual
- `TASK_057`: validar en TV fisica failover/audio para cierre final de `BUG_022`.
- Opcional hardening de `TASK_059`: validar con mas bouquets reales y rotar API key expuesta en pruebas.

## Riesgo/pendiente externo
- Configuracion externa XUI sigue siendo dependencia critica (access code/api key/permisos).
- El panel puede variar en nombre exacto de parametros de paquete; endpoint soporta `xuiCreateParams` para mitigar diferencias.
