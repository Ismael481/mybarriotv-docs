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
