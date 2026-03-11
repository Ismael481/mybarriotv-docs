# TASK_056_auditoria_stream_cortes_audio_xui_player

Estado: in_progress

Fecha de creacion:
2026-03-11

Ultima actualizacion:
2026-03-11

## Objetivo
Identificar por que el playback se corta con buena conexion y por que en algunos streams hay video sin audio, revisando XUI + backend + TV player sin mezclar implementaciones aun.

## Motivo
Usuario reporta:
- cortes frecuentes de stream,
- casos de video sin audio,
- necesidad de revisar juntos paso a paso con evidencia concreta.

## Alcance
- Inspeccion de implementacion del player en TV:
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
  - `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
  - `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- Inspeccion del bridge playback:
  - `backend/src/xuiClient.js`
  - `backend/src/server.js`
- Verificaciones runtime contra endpoints reales (`/v1/content/home`, `/v1/content/:id/playback`) y probes de stream con `curl`/`ffprobe`.
- Actualizacion documental en indices, bug y changelog.

## No tocar
- Arquitectura global del producto.
- Refactor amplio.
- Nuevas features no relacionadas con incidencia de playback.

## Archivos involucrados
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/PlayerFactory.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerImpl.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- `backend/src/xuiClient.js`
- `backend/src/server.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_056_auditoria_stream_cortes_audio_xui_player.md`
- `docs/03_bugs/BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Dependencias
- Bridge Xtream previamente activo (`TASK_052`).
- Runtime local con `XUI_MODE=xtream` y `XUI_OUTPUT=ts`.

## Implementacion realizada
- Auditoria de codigo y runtime (sin cambios funcionales).
- Se confirmo en backend local:
  - `GET /v1/content/37/playback` -> URL TS firmada que termina en `302 -> 200`.
  - `GET /v1/content/41/playback` -> URL TS firmada que termina en `302 -> 404`.
- Se confirmo con probes:
  - canales con `h264 + aac`,
  - canales con `h264 + ac3`,
  - canales donde el flujo falla en upstream (404) aunque el contrato backend devuelve URL.
- Se confirmo en codigo TV:
  - `ExoPlayer` creado con defaults,
  - no hay `trackSelector` avanzado ni telemetria por codec/pista,
  - reconexion runtime existe, pero sin diagnostico de causa (codec, upstream, timeout).

## Cambios manuales externos
- Requeridos en XUI/panel para resolver incidencia completa:
  - revisar canales que redirigen a `/auth/...` y terminan en `404`,
  - revisar politica de audio para minimizar incompatibilidades (priorizar AAC donde aplique).

## Riesgos
- Incidencia multi-causa: parte backend/upstream y parte compatibilidad/observabilidad en player.
- Si solo se corrige app sin ajustar XUI, seguiran fallos de canales que hoy terminan en `404`.
- Si solo se corrige XUI sin instrumentar player, seguira baja trazabilidad de audio/cortes en TV.

## Pruebas requeridas por el usuario
- Validar en TV fisica 3 casos:
  - canal estable AAC,
  - canal AC3,
  - canal con 404 aguas arriba.
- Capturar logs de player en el instante exacto de "video sin audio".

## Criterio de exito
- Causas probables priorizadas y documentadas con evidencia reproducible.
- Siguiente paso tecnico minimo acordado (sin mezclar cambios amplios).

## Pendientes
- Definir el parche minimo de la siguiente iteracion (player y/o backend).
- Ejecutar validacion manual en TV con lista corta de canales representativos.
