# CHANGELOG 2026 Q1

## 2026-03-06
- Se reorganizo la base del repositorio hacia estructura monorepo con la app TV ubicada en `apps/tv-app/`.
- Se crearon los espacios reservados `apps/web-app/` y `backend/` con README inicial.
- Se completo la documentacion base de contexto, estado, tarea activa, alcance, arquitectura y reglas operativas.
- Se documentaron las decisiones ADR sobre estructura monorepo y rol de XUI como motor de contenido.
- Se ejecuto una auditoria tecnica inicial de la app TV existente sin cambios funcionales de producto.
- Se documento `TASK_002_xui_first_bridge` con el primer flujo minimo recomendado: Home catalog read-only + seleccion de item + playback de prueba via backend.
- Se agrego `ADR_003_first_bridge_read_only_home_catalog` para fijar el alcance tecnico minimo del primer bridge `App TV -> Backend -> XUI`.
- Se actualizaron `CHATGPT_CONTEXT`, `CURRENT_STATUS` y `ACTIVE_TASK` para reflejar el nuevo estado documental.
- Se implemento backend minimo del bridge con endpoints `GET /v1/content/home` y `GET /v1/content/:id/playback` en `backend/src/server.js`.
- Se agrego cliente XUI y mapper de contrato estable en `backend/src/xuiClient.js` y `backend/src/mappers.js`.
- Se integro TV app al backend con feature flag `BridgeEnabled` y base URL configurable `BACKEND_BASE_URL`.
- Se incorporo repositorio bridge en app para Home y playback: `BridgeCatalogRepository`, `BackendBridgeApi`, `PlayerViewModel`.
- Se mantuvo fallback a `DemoCatalogRepository` cuando `BridgeEnabled=false`.
- Se verifico inconsistencia de sync sobre ADR_003: el archivo existe y la ruta oficial es `docs/04_decisions/ADR_003_first_bridge_read_only_home_catalog.md`.
- Se agrego `TASK_003_adr003_sync_fix` y commit documental para forzar nueva sincronizacion del mirror publico.
- Se habilito `android:usesCleartextTraffic="true"` en la TV app para permitir pruebas LAN HTTP contra backend local.
- Se valido E2E en TV fisica: Home carga `test1` desde backend y playback reproduce stream real de XUI.
- Se documento dependencia operativa externa: el stream de XUI puede requerir restart manual cuando se pausa/cae.

## 2026-03-07
- Se implemento `TASK_004_bridge_stream_stabilization` para mejorar resiliencia del playback bridge.
- Backend: nuevo endpoint `GET /v1/bridge/health` para check de salud del bridge/upstream.
- Backend: clasificacion de errores upstream (`TIMEOUT`, `STREAM_UNAVAILABLE`, `INVALID_RESPONSE`, `UPSTREAM_UNREACHABLE`, `UPSTREAM_HTTP_ERROR`).
- Backend: retry controlado configurable (`BRIDGE_MAX_RETRIES`) con logs de fallo/retry/resultado.
- TV app: manejo de error de playback con estado controlado y retry manual limitado (max 2).
- TV app: deteccion de fallo de player al inicio mediante listener de error de ExoPlayer.
- TV app: indicador visible de `buffering/reconnecting` (bolita girando) durante recuperacion temporal del stream.
- TV app: reconexion runtime controlada en player (auto-retry corto y `Play` fuerza `prepare()` cuando queda en estado de recuperacion).
- TV app: si `Play` no retoma stream en ventana corta, se ejecuta reconexion forzada para evitar estado congelado.
- TV app: mensajes de error/carga/reconexion del player traducidos a espanol para validacion en TV fisica.
- Se implemento `TASK_005_bridge_operational_monitoring` para monitoreo operativo basico previo a login.
- Backend: nuevo endpoint `GET /v1/bridge/ops` con estado resumido del bridge.
- Backend: snapshot operativo en memoria con `lastCheck`, `lastSuccessAt`, `lastError` y contadores (`checks/success/failures/retries`).
- Se mantuvo contrato estable de Home/Playback sin cambios para TV app.
- TASK_005 validada en local en caso sano y caso degradado (upstream 404 clasificado como `STREAM_UNAVAILABLE`).
- Se implemento `TASK_006_authentication_foundation` con auth minima previa a logica comercial y device binding.
- Backend: nuevo modulo `backend/src/auth.js` con validacion de credenciales, JWT HS256, parser JSON y middleware de auth.
- Backend: nuevos endpoints `POST /v1/auth/login`, `GET /v1/auth/me` y `GET /v1/auth/protected` (los dos ultimos protegidos con JWT).
- Backend: nuevas variables env de auth en `.env.example` y documentacion actualizada en `backend/README.md`.
- TV app: nuevo cliente auth `BackendAuthApi` y `LoginViewModel` para login real con backend.
- TV app: login screen conectado a backend con estado de carga/error y persistencia de token en `UserSession`.
- TV app: guard de navegacion para bloquear Home sin sesion y redireccionar a Login.
- TV app: interceptor de auth activado en `HttpClient` para enviar `Authorization: Bearer <token>` en requests.
