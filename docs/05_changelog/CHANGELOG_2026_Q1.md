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
