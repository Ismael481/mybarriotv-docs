# CURRENT_STATUS

## Estado general
- Bridge minimo `App TV -> Backend -> XUI` sigue operativo y validado en TV fisica.
- Home y playback funcionan en estado sano.
- Se implemento fase de estabilizacion de stream para fallos temporales upstream.

## Resultado de TASK_004
- Backend con health endpoint del bridge: `GET /v1/bridge/health`.
- Backend con clasificacion de errores (`TIMEOUT`, `STREAM_UNAVAILABLE`, `INVALID_RESPONSE`, `UPSTREAM_UNREACHABLE`, `UPSTREAM_HTTP_ERROR`).
- Backend con retry controlado configurable (`BRIDGE_MAX_RETRIES`).
- Logs backend mejorados para trazabilidad de inicio/fallo/retry/resultado.
- App TV con estado de error de playback controlado, retry manual limitado y indicador visible de buffering/reconnecting en player.
- App TV con reconexion runtime controlada (auto-retry corto) cuando el stream cae durante reproduccion.
- Mensajes visibles de playback/reconexion actualizados a espanol para pruebas en TV fisica.

## Dependencias externas actuales
- La disponibilidad final del stream depende del estado operativo de XUI/origen.
- Si stream cae en XUI, puede requerir restart operativo en panel, aunque ahora el bridge responde de forma controlada.
- Si XUI devuelve pantalla propia de canal offline, ese comportamiento es upstream y no se controla desde la app.

## Siguiente enfoque recomendado
- Monitoreo operativo basico de streams y politicas de alerta.
- Luego continuar a fase de autenticacion.
