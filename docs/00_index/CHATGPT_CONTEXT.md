# CHATGPT_CONTEXT

Fecha: 2026-03-11
Rama: `main`
Tarea activa: `TASK_056_auditoria_stream_cortes_audio_xui_player`

## Resumen operativo
- `TASK_056` audita incidencia reportada por usuario: cortes de stream con buena conexion y casos de video sin audio.
- Evidencia de upstream XUI:
  - canal estable ejemplo `contentId=37`: redireccion `302` y respuesta final `200` con flujo TS.
  - canal fallido ejemplo `contentId=41`: redireccion `302` y respuesta final `404`.
- Evidencia de audio:
  - canales con `aac` (compatibilidad alta),
  - canales con `ac3` (riesgo de silencio segun decoder del TV).
- Evidencia de codigo TV:
  - ExoPlayer inicializado con configuracion por defecto,
  - sin selector avanzado de pistas/audio ni telemetria de codec en runtime.
- `TASK_055` queda sin cambios en esta iteracion.

## Archivos clave de la tarea activa
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion minima
- Confirmar reproduccion de evidencia en backend:
  - `GET /v1/content/37/playback` y verificacion de respuesta final `200` al consumir stream.
  - `GET /v1/content/41/playback` y verificacion de respuesta final `404` en upstream.
- Confirmar mezcla de codecs (`aac` y `ac3`) con `ffprobe` sobre URLs reales.
- Consistencia documental entre indices + `TASK_056` + `BUG_022`.

## Cambios manuales externos requeridos
- Si se desea estabilizar todos los canales, se requieren ajustes manuales en XUI/origen:
  - corregir canales que terminan en `404` tras redireccion `/auth/...`,
  - revisar politica de audio para minimizar canales solo AC3 en dispositivos no compatibles.
- Validar en TV fisica con los canales marcados en `BUG_022`.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
