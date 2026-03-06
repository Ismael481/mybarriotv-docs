# CURRENT_STATUS

## Estado general
- Monorepo activo con `apps/tv-app`, `apps/web-app`, `backend` y `docs`.
- Se implemento el primer bridge minimo `App TV -> Backend -> XUI` para Home y playback.
- El backend ya expone endpoints `GET /v1/content/home` y `GET /v1/content/:id/playback`.
- La TV app incorpora feature flag `BridgeEnabled` para alternar demo/backend.

## Arquitectura vigente en esta fase
- Arquitectura objetivo: `App TV -> Backend propio -> XUI`.
- Regla activa: no hay integracion directa `App TV -> XUI`.
- XUI sigue como motor de contenido; backend funciona como capa de adaptacion y control.

## Resultado de la fase actual
- Backend minimo funcional con cliente XUI, mapper y logs de bridge.
- TV app consumiendo backend para Home/playback cuando `BridgeEnabled=true`.
- Fallback demo preservado cuando `BridgeEnabled=false`.

## Bloqueos o dependencias externas actuales
- Validar payload real de XUI y ajustar mapper si hay diferencias.
- Ejecutar prueba E2E en entorno con stream real.
- Entorno local no pudo compilar Android por falta de JBR (`jvm.cfg` de Android Studio).
