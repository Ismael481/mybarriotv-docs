# ACTIVE_TASK

Tarea activa unica: **TASK_056_auditoria_stream_cortes_audio_xui_player**

Estado: `in_progress`

Objetivo:
- Auditar por que el stream se detiene aun con buena conexion y por que algunos canales muestran video sin audio.
- Consolidar causa probable y evidencias en TV app + backend + XUI antes de aplicar fixes.

Alcance:
- Inspeccion de player TV en:
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
  - `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- Inspeccion de bridge playback en:
  - `backend/src/xuiClient.js`
  - `backend/src/server.js`
- Verificacion operativa de URLs reales de playback con probes HTTP/ffprobe.
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Restricciones:
- No mezclar con refactor amplio ni nuevas features.
- No tocar arquitectura global del producto.
- No cerrar hallazgos sin documentar cambios manuales externos requeridos en XUI.

Criterio de exito:
- Quedan causas probables priorizadas con evidencia reproducible.
- Queda definido el siguiente paso tecnico minimo para correccion.

Prueba minima:
1. Confirmar un canal que responde `302 -> 200` y otro que responde `302 -> 404` en URL de playback devuelta por backend.
2. Confirmar mezcla de codecs de audio reales (`aac` y `ac3`) en muestra de canales.
3. Verificar consistencia entre `ACTIVE_TASK`, `CURRENT_STATUS`, `CHATGPT_CONTEXT`, `TASK_056`, `BUG_022` y changelog.
