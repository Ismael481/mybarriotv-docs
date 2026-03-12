# TASK_057_playback_failover_y_hardening_audio_stream

Estado: completed

Fecha de creacion:
2026-03-11

Ultima actualizacion:
2026-03-12

## Objetivo
Mitigar cortes de stream y casos de video sin audio sin romper el flujo actual, aplicando failover controlado y hardening minimo de player/backend.

## Motivo
`TASK_056` confirmo incidencia multi-causa:
- URLs upstream intermitentes/fallidas.
- Mezcla de codecs de audio.
- Observabilidad limitada en el player para recuperacion automatica.

## Alcance
- Backend playback Xtream:
  - construir URL primaria + alternativas ordenadas.
  - exponer alternativas como `fallbackPlaybackUrls` sin romper contrato existente.
- TV app:
  - consumir `fallbackPlaybackUrls`.
  - failover automatico de source URL ante error runtime.
- ExoPlayer:
  - habilitar decoder fallback.
  - fortalecer DataSource HTTP (redirects + timeouts).

## No tocar
- Arquitectura global del producto.
- Refactor amplio fuera del flujo de playback.
- Logica comercial/auth no relacionada con la incidencia.

## Archivos involucrados
- `backend/src/xuiClient.js`
- `backend/src/mappers.js`
- `backend/test/playback-contract.test.js`
- `backend/README.md`
- `apps/tv-app/gradle/libs.versions.toml`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeDtos.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/BridgeCatalogRepository.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerStateListener.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
- `docs/02_tasks/TASK_057_playback_failover_y_hardening_audio_stream.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- Backend:
  - `xuiClient` genera lista ordenada de candidatos (signed ts/hls + live ts/hls) y devuelve `fallbackPlaybackUrls`.
  - `mappers` normaliza/depura `fallbackPlaybackUrls` (sin duplicados y sin incluir primaria).
  - nuevo test `playback-contract.test.js` para contrato de fallback.
- TV app:
  - DTO bridge incorpora `fallbackPlaybackUrls`.
  - repositorio bridge expone `BridgePlaybackSource` (primaria + fallback).
  - `PlayerViewModel` implementa failover automatico al siguiente source si falla playback runtime.
- ExoPlayer:
  - `DefaultRenderersFactory.setEnableDecoderFallback(true)`.
  - DataSource HTTP explicito con redirects permitidos y timeouts controlados.
  - error de player ahora incluye `errorCodeName` para mejor diagnostico.
- Fix de compilacion:
  - se agrega `androidx.media3:media3-exoplayer-hls` al bundle `media3` del version catalog para resolver `HlsMediaSource`.
  - se corrige import en `ExoPlayerImpl.kt` a `androidx.media3.exoplayer.hls.HlsMediaSource`.

## Cambios manuales externos
- Puede requerirse ajuste en XUI para casos puntuales:
  - corregir canales con token/redireccion que terminen en `404`.
  - revisar codecs de audio de canales problematicos en TVs sin soporte AC3.

## Riesgos
- Si un canal no tiene alternativa realmente util en upstream, el failover no lo recuperara.
- Puede existir mayor latencia inicial en resolucion de playback por busqueda de fuentes alternativas.
- Persisten diferencias de compatibilidad por modelo de TV/decoder de audio.

## Pruebas requeridas por el usuario (cumplidas)
- Backend:
  - `npm test` en verde.
- Operativa:
  - validar al menos 1 canal donde falle primaria y recupere con fallback.
- TV fisica:
  - validar canales AAC y AC3 para confirmar mejora real de audio/reproduccion.

## Resultado de pruebas ejecutadas
- `npm test` backend: OK (`8` tests, `0` fallos).
- `npm test` backend re-ejecutado tras fix de import HLS: OK (`8` tests, `0` fallos).
- Probe operativo (script local):
  - caso `contentId=50`: primaria signed `/play/.../ts` puede fallar (`404`) y alternativa `/live/.../50.ts` responde con streams validos (`h264 + mp2`).
- Compilacion TV app:
  - bloqueada por entorno local (`C:\Program Files\Android\Android Studio\jbr\lib\jvm.cfg` no disponible en esta maquina).
  - No bloquea el cierre porque la validacion manual en TV fisica fue completada con resultado satisfactorio.
- Validacion manual en TV fisica (usuario):
  - failover operativo confirmado en reproduccion real.
  - audio y reproduccion reportados como estables y satisfactorios.

## Criterio de exito
- Mitigacion aplicada sin romper contrato actual.
- Validacion tecnica automatizada backend en verde.
- Validacion manual en TV fisica completada con resultado positivo.
- Criterio de exito cumplido.

## Estado de cierre formal
- Validacion manual en TV fisica completada por el usuario con resultado positivo y estable.
- Criterio de exito cumplido en failover, reproduccion y audio.
- Bloqueos abiertos: ninguno.

## Observaciones opcionales (no bloqueantes)
- Si reaparecen canales concretos con `404` en upstream, documentar ajuste puntual requerido en XUI por canal.
- Mantener monitoreo operativo pasivo de codecs/compatibilidad por modelo de TV.

