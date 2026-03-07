# CHATGPT_CONTEXT

Fecha:
2026-03-07

Rama actual:
main

Ultimo commit:
aec5ee9

Tarea activa:
TASK_004_bridge_stream_stabilization

Resumen corto:
Se implemento una fase pequena de estabilizacion del bridge de playback. Backend ahora clasifica fallos upstream y ejecuta retry controlado. App TV ahora muestra buffering/reconnecting visible, retry manual y reconexion runtime acotada del player. Home no se rompio.
Mensajes de UI del player para carga/reconexion/error fueron ajustados a espanol.

Archivos modificados:
- backend/src/xuiClient.js
- backend/src/server.js
- backend/.env.example
- backend/README.md
- apps/tv-app/libs/player/src/main/java/com/techlads/player/domain/state/PlayerState.kt
- apps/tv-app/libs/exoplayer/src/main/java/com/techlads/exoplayer/ExoPlayerStateListener.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt
- docs/00_index/CHATGPT_CONTEXT.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_004_bridge_stream_stabilization.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_004_bridge_stream_stabilization.md
- docs/02_tasks/TASK_002_xui_first_bridge.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Pendiente de prueba por el usuario:
- Probar caso normal y caso de stream interrumpido en TV fisica.
- Verificar retry manual de playback (max 2) y clasificacion de errores backend.

Riesgos o bloqueos:
- Si XUI/origen cae, la disponibilidad sigue dependiendo del proveedor, aunque ahora la falla queda controlada y trazable.
- Si XUI entrega su propia pantalla de "channel offline", la app no puede forzar spinner continuo sin ajuste operativo en XUI.

Cambios manuales externos requeridos:
- Operacion de XUI: restart manual de stream cuando quede pausado/caido.
- Rotacion de credenciales de linea de prueba luego de pruebas.
