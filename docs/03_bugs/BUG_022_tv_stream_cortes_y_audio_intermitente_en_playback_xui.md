# BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui

Estado:
partial

Fecha de deteccion:
2026-03-11

Ultima actualizacion:
2026-03-11

Descripcion:
En TV se reportan cortes de stream frecuentes y casos donde el video reproduce pero el audio no se escucha.

Sintomas:
- Playback que se detiene aun con buena conexion de internet.
- Canales que muestran video sin audio.
- Comportamiento inconsistente entre canales del mismo catalogo.

Contexto:
- Flujo activo: `App TV -> Backend (/v1/content/:id/playback) -> XUI Xtream`.
- Runtime local en modo `xtream` con `XUI_OUTPUT=ts`.
- Reproduccion en TV via ExoPlayer (Media3) con fuente TS firmada.

Causa probable:
- Causa 1 (upstream XUI): algunas URLs firmadas resuelven `302 -> 404` despues de redireccion `/auth/...` (ejemplo detectado: `contentId=41`).
- Causa 2 (compatibilidad audio): mezcla de codecs (`aac`, `ac3`, `mp2`) en streams reales; `ac3` puede derivar en video sin audio segun decoder del TV.
- Causa 3 (observabilidad player): configuracion de ExoPlayer en defaults, sin diagnostico detallado de pista/audio codec para diferenciar fallo de codec vs fallo de origen.

Archivos revisados:
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- `backend/src/xuiClient.js`
- `backend/src/server.js`

Solucion aplicada:
- Backend ahora devuelve `fallbackPlaybackUrls` junto a `playbackUrl` para permitir failover sin romper contrato anterior.
- TV app ahora intenta automaticamente la siguiente URL de playback cuando falla la actual.
- ExoPlayer se endurece con `decoder fallback` y DataSource HTTP con redirects/timeouts explicitos.
- Se agrega prueba automatizada backend del contrato de fallback (`playback-contract.test.js`).

Pendiente de validacion:
- Probar en TV fisica canales AAC vs AC3 y confirmar mejora en audio/estabilidad.
- Confirmar en TV caso real de failover (primaria falla, alternativa recupera).
- Validar en XUI/panel los canales que sigan terminando en `404` tras redireccion `/auth/...`.

Resultado esperado tras la correccion:
- Reduccion de cortes por canales con URL upstream invalida.
- Menor incidencia de video sin audio por failover + decoder fallback y/o ajuste en XUI.
- Trazabilidad clara para distinguir fallo de origen, fallo de codec o fallo de red.
