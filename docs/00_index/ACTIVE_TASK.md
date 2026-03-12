# ACTIVE_TASK

Tarea activa unica: **TASK_057_playback_failover_y_hardening_audio_stream**

Estado: `in_progress`

Objetivo:
- Mitigar cortes de stream y casos de video sin audio sin romper el flujo actual.
- Aplicar failover controlado de URLs y hardening minimo del player para mejorar resiliencia.

Alcance:
- Backend playback:
  - `backend/src/xuiClient.js`
  - `backend/src/mappers.js`
  - `backend/test/playback-contract.test.js`
  - `backend/README.md`
- TV app player/bridge en:
  - `apps/tv-app/gradle/libs.versions.toml`
  - `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeDtos.kt`
  - `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/BridgeCatalogRepository.kt`
  - `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt`
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
  - `docs/00_index/ACTIVE_TASK.md`
  - `docs/00_index/CURRENT_STATUS.md`
  - `docs/00_index/CHATGPT_CONTEXT.md`
  - `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
  - `docs/02_tasks/TASK_057_playback_failover_y_hardening_audio_stream.md`
  - `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
  - `docs/05_changelog/CHANGELOG_2026_Q1.md`

Restricciones:
- No refactor amplio.
- No cambios de arquitectura global.
- No introducir cambios destructivos en contrato existente de playback.

Criterio de exito:
- Backend entrega URL primaria + alternativas de failover sin romper clientes actuales.
- TV app intenta URL alternativa si falla la primaria.
- Validacion tecnica automatizada backend en verde.
- Queda pendiente solo validacion manual en TV para cierre final de bug.

Prueba minima:
1. Ejecutar `npm test` en `backend/` y confirmar suite en verde (incluyendo test de `fallbackPlaybackUrls`).
2. Verificar con probe que un canal con URL primaria fallida pueda tener URL alternativa util (ej: fallback a `/live/...ts`).
3. Confirmar consistencia de indices + `TASK_057` + `BUG_022` + changelog.
