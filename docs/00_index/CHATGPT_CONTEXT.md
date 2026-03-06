# CHATGPT_CONTEXT

Fecha:
2026-03-06

Rama actual:
main

Ultimo commit:
a8eded0

Tarea activa:
TASK_002_xui_first_bridge

Resumen corto:
Se implemento la primera version funcional minima del bridge `App TV -> Backend -> XUI` para Home catalog minimo y playback de prueba. El backend consulta XUI y devuelve contrato estable. La TV app cambia entre demo y backend con `BridgeEnabled`.

Archivos modificados:
- backend/package.json
- backend/.env.example
- backend/README.md
- backend/src/server.js
- backend/src/xuiClient.js
- backend/src/mappers.js
- apps/tv-app/app/build.gradle.kts
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeDtos.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeApi.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/BridgeCatalogRepository.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/details/ProductViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt
- docs/00_index/CHATGPT_CONTEXT.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_002_xui_first_bridge.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_002_xui_first_bridge.md
- docs/04_decisions/ADR_002_xui_as_content_engine.md
- docs/04_decisions/ADR_003_first_bridge_read_only_home_catalog.md

Pendiente de prueba por el usuario:
- Validar E2E real de Home + playback con backend y XUI.
- Confirmar reproduccion de stream >= 10s con `BridgeEnabled=true`.

Riesgos o bloqueos:
- Mapper backend podria requerir ajustes segun payload real de XUI.
- No fue posible compilar app Android en este entorno por falta de JBR local.

Cambios manuales externos requeridos:
- Configurar variables de backend (`XUI_BASE_URL`, opcional `XUI_API_KEY`, paths XUI).
- Asegurar en XUI endpoints operativos de Home y playback con item reproducible.
