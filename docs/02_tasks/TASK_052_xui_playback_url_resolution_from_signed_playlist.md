# TASK_052_xui_playback_url_resolution_from_signed_playlist

Estado: completed

Fecha:
2026-03-09

## Objetivo
Corregir la URL de playback del bridge XUI para paneles que entregan streams firmados por token.

## Alcance
- Backend `xuiClient` en modo `xtream`.
- Ajuste runtime local en `backend/.env`.
- Sin cambios de arquitectura global.

## Archivos tocados
- `backend/src/xuiClient.js`
- `backend/.env` (local, ignorado por git)
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_052_xui_playback_url_resolution_from_signed_playlist.md`
- `docs/03_bugs/BUG_021_xtream_live_url_not_playable_for_signed_panel_streams.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Matching por nombre de canal depende de consistencia entre `get_live_streams` y playlist M3U.
- Si hay nombres duplicados, puede requerir regla adicional de desambiguacion.

## Pendiente de prueba
- Validacion final en TV fisica con varios canales reales.

## Resultado esperado
- `/v1/content/:id/playback` devuelve URL firmada reproducible (`/play/<token>/m3u8`) cuando el panel la provee.
- En entorno local actual se deja `XUI_OUTPUT=ts` para devolver `/play/<token>/ts` y maximizar compatibilidad del player TV.

## Pasos manuales si existen
1. Abrir Home en TV y seleccionar varios canales.
2. Confirmar inicio de playback sin error de stream.
3. Si falla un canal puntual, capturar `stream_id` para revisar match en playlist.
