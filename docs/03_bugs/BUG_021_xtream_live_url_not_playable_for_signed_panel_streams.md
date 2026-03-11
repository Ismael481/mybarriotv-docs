# BUG_021_xtream_live_url_not_playable_for_signed_panel_streams

Estado: closed
Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Sintoma reportado
- En TV el playback fallaba con error de reproductor aunque Home cargaba contenido.

## Causa raiz
- El backend devolvia URLs `.../live/<user>/<pass>/<id>.m3u8`.
- En este panel XUI, los streams se entregan como URLs firmadas `.../play/<token>/m3u8` (visibles en playlist M3U).

## Correccion aplicada
- `backend/src/xuiClient.js` ahora:
  - lee playlist Xtream `.../playlist/<user>/<pass>/m3u?output=hls`,
  - parsea `#EXTINF` + URL,
  - busca URL por nombre de stream y la usa como playback primario.
- Mantiene fallback a `/live/...` si no encuentra match.
- Runtime local: `XUI_API_KEY` vacio en modo xtream para evitar interferencia.

## Archivos tocados
- `backend/src/xuiClient.js`
- `backend/.env` (local)
- `docs/03_bugs/BUG_021_xtream_live_url_not_playable_for_signed_panel_streams.md`

## Validacion
- `GET /v1/content/31/playback` ahora devuelve URL firmada `http://panel.mybarriotv.com:80/play/.../m3u8`.
- `npm test` backend OK (`6` pruebas, `0` fallos).

## Paso manual externo requerido
- Validar en TV fisica reproduccion de multiples canales.

