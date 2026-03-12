# TASK_057_playback_failover_y_hardening_audio_stream

Estado: in_progress

Fecha de creacion:
2026-03-11

Ultima actualizacion:
2026-03-11

## Objetivo
Mitigar cortes de stream y casos de video sin audio sin romper el flujo actual, aplicando failover controlado y hardening minimo de player/backend.

## Motivo
`TASK_056` confirmo incidencia multi-causa:
- URLs upstream intermitentes/fallidas,
- mezcla de codecs de audio,
- observabilidad limitada en el player para recuperación automática.

## Alcance
- Backend playback Xtream:
  - construir URL primaria + alternativas ordenadas,
  - exponer alternativas como `fallbackPlaybackUrls` sin romper contrato existente.
- TV app:
  - consumir `fallbackPlaybackUrls`,
  - failover automático de source URL ante error runtime.
- ExoPlayer:
  - habilitar decoder fallback,
  - fortalecer DataSource HTTP (redirects + timeouts).

## No tocar
- Arquitectura global del producto.
- Refactor amplio fuera del flujo de playback.
- Lógica comercial/auth no relacionada con la incidencia.

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
  - `xuiClient` ahora genera lista ordenada de candidatos (signed ts/hls + live ts/hls) y devuelve `fallbackPlaybackUrls`.
  - `mappers` normaliza/depura `fallbackPlaybackUrls` (sin duplicados y sin incluir primaria).
  - nuevo test `playback-contract.test.js` para contrato de fallback.
- TV app:
  - DTO bridge incorpora `fallbackPlaybackUrls`.
  - repositorio bridge expone `BridgePlaybackSource` (primaria + fallback).
  - `PlayerViewModel` implementa failover automático al siguiente source si falla playback runtime.
- ExoPlayer:
  - `DefaultRenderersFactory.setEnableDecoderFallback(true)`.
  - DataSource HTTP explícito con redirects permitidos y timeouts controlados.
  - error de player ahora incluye `errorCodeName` para mejor diagnóstico.
- Fix de compilación:
  - se agrega `androidx.media3:media3-exoplayer-hls` al bundle `media3` del version catalog para resolver `HlsMediaSource`.
  - se corrige import en `ExoPlayerImpl.kt` a `androidx.media3.exoplayer.hls.HlsMediaSource`.

## Cambios manuales externos
- Puede requerirse ajuste en XUI para cierre total:
  - corregir canales con token/redirección que terminen en `404`,
  - revisar códecs de audio de canales problemáticos en TVs sin soporte AC3.

## Riesgos
- Si un canal no tiene alternativa realmente util en upstream, el failover no lo recuperará.
- Puede existir mayor latencia inicial en resolución de playback por búsqueda de fuentes alternativas.
- Persisten diferencias de compatibilidad por modelo de TV/decoder de audio.

## Pruebas requeridas por el usuario
- Backend:
  - `npm test` en verde.
- Operativa:
  - validar al menos 1 canal donde falle primaria y recupere con fallback.
- TV física:
  - validar canales AAC y AC3 para confirmar mejora real de audio/reproducción.

## Resultado de pruebas ejecutadas
- `npm test` backend: OK (`8` tests, `0` fallos).
- `npm test` backend re-ejecutado tras fix de import HLS: OK (`8` tests, `0` fallos).
- Probe operativo (script local):
  - caso `contentId=50`: primaria signed `/play/.../ts` puede fallar (`404`) y alternativa `/live/.../50.ts` responde con streams válidos (`h264 + mp2`).
- Compilación TV app:
  - bloqueada por entorno local (`C:\\Program Files\\Android\\Android Studio\\jbr\\lib\\jvm.cfg` no disponible en esta máquina).

## Criterio de exito
- Mitigación aplicada sin romper contrato actual.
- Validación técnica automatizada backend en verde.
- Bug pasa a `partial` hasta validar manualmente en TV y confirmar recuperación real de usuario.

## Pendientes
- Ejecutar validación manual en TV física y cerrar `BUG_022` si la mitigación confirma estabilidad/audio.
- Si persisten canales concretos fallando, documentar ajuste manual requerido en XUI por canal.
