# TASK_004_bridge_stream_stabilization

Fecha: 2026-03-07
Estado: implementada

## Objetivo
Mejorar resiliencia del playback bridge (`App TV -> Backend -> XUI`) ante cortes temporales de stream/upstream, sin abrir alcance de autenticacion ni logica comercial.

## Problema real observado
- El bridge minimo ya funcionaba en TV fisica, pero en algunos momentos el stream de XUI quedaba pausado/caido y requeria restart manual en panel.
- En esos casos faltaba clasificacion clara de fallos en backend y manejo controlado de error/reintento en app.

## Alcance implementado
### Backend
- Se agrego endpoint de health del bridge:
  - `GET /v1/bridge/health`
- Se implemento clasificacion de errores upstream:
  - `TIMEOUT`
  - `STREAM_UNAVAILABLE`
  - `INVALID_RESPONSE`
  - `UPSTREAM_UNREACHABLE`
  - `UPSTREAM_HTTP_ERROR`
- Se implemento retry controlado (sin bucle agresivo) con `BRIDGE_MAX_RETRIES`.
- Se mejoraron logs para distinguir:
  - inicio de consulta upstream
  - fallo upstream
  - retry ejecutado
  - respuesta final
- Se mantuvo contrato estable hacia la app en `/v1/content/home` y `/v1/content/{id}/playback`.

### TV app
- Se mejoro manejo de error de playback request:
  - estado de error controlado
  - retry manual simple y acotado (max 2)
- Se agrego deteccion de fallo de player al inicio mediante listener de error de ExoPlayer.
- Se agrego indicador visible de `buffering/reconnecting` (bolita girando) sobre el reproductor durante cortes temporales.
- Se agrego reconexion runtime controlada en el player ante error de stream:
  - hasta 3 reintentos automaticos con espera corta
  - `Play` vuelve a disparar `prepare()` si quedo en estado de reconexion
  - si `Play` no recupera reproduccion en ventana corta, se fuerza reconexion automaticamente
- Se ajustaron mensajes de playback a espanol para estado de carga, reconexion, error y accion de reintento.
- No se rompieron Home ni navegacion del bridge actual.

## Fuera de alcance (respetado)
- Autenticacion, sesiones, tokens, device binding.
- Panel web, portal cliente, logica comercial.
- Refactor grande o cambios visuales amplios.

## Mitigacion aplicada
1. Backend resiliente: clasifica fallos y aplica retry solo en condiciones retryables.
2. App resiliente: si falla resolucion de playback o falla inicio de reproduccion, muestra error controlado y permite retry limitado.
3. UX resiliente: mientras el stream se recupera, el usuario ve estado de carga/reconexion en player.
4. Operacion: se mantiene trazabilidad de si el problema viene de app, backend o upstream XUI.

## Archivos tocados
- `backend/src/xuiClient.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/libs/player/src/main/java/com/techlads/player/domain/state/PlayerState.kt`
- `apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerStateListener.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- `docs/02_tasks/TASK_004_bridge_stream_stabilization.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Como probar
### Caso normal
1. Levantar backend con env Xtream validos.
2. Abrir Home en TV con `BridgeEnabled=true`.
3. Seleccionar `test1`.
4. Confirmar playback estable.

### Caso stream interrumpido
1. Interrumpir o pausar stream en XUI.
2. Intentar reproducir desde TV.
3. Verificar:
   - backend devuelve error clasificado (`STREAM_UNAVAILABLE` o relacionado)
   - app muestra estado de carga/reconexion (spinner) mientras intenta recuperar
   - si no recupera, app muestra estado de error controlado
   - boton Retry disponible (hasta 2 intentos)
4. Reiniciar stream en XUI y usar Retry.
5. Confirmar recuperacion.

## Dependencia que permanece en XUI
- La disponibilidad real del stream sigue dependiendo del estado operativo de XUI/origen m3u.
- Si el stream cae en origen, backend/app mitigan y reportan mejor, pero no reemplazan control operativo de XUI.
- Si XUI inyecta pantalla propia de "channel offline", esa salida viene del upstream y no puede ser ocultada desde backend/app sin cambiar la configuracion/politica de XUI.

## Que sigue despues
- Tarea siguiente recomendada: endurecer monitoreo operativo (health stream + alerta basica) antes de abrir login/autenticacion.
