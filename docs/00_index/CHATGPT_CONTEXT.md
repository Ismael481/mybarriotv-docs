# CHATGPT_CONTEXT

Fecha:
2026-03-06

Rama actual:
work

Último commit:
65cbb0d

Tarea activa:
TASK_001_initial_app_audit

Resumen corto:
Se reorganizó el repo a base monorepo (`apps/tv-app`, `apps/web-app`, `backend`, `docs`) y se documentó una auditoría técnica inicial de la TV app sin integrar XUI real ni cambiar comportamiento funcional.

Archivos modificados:
- movimiento estructural `app/tv-app` -> `apps/tv-app`
- apps/web-app/README.md
- backend/README.md
- docs/00_index/CHATGPT_CONTEXT.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/01_core/PROJECT_SCOPE.md
- docs/01_core/ARCHITECTURE.md
- docs/01_core/PROMPT_RULES.md
- docs/02_tasks/TASK_001_initial_app_audit.md
- docs/04_decisions/ADR_001_monorepo_structure.md
- docs/04_decisions/ADR_002_xui_as_content_engine.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revisión por ChatGPT:
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_001_initial_app_audit.md
- docs/04_decisions/ADR_001_monorepo_structure.md
- docs/04_decisions/ADR_002_xui_as_content_engine.md

Pendiente de prueba por el usuario:
- validar que la estructura nueva del repo quedó correcta
- confirmar que la app TV sigue reconocible dentro de `apps/tv-app/`
- revisar documentación inicial creada

Riesgos o bloqueos:
- posible necesidad de ajustar rutas en scripts externos/CI que antes apuntaban a `app/tv-app`
- integración real con XUI y backend sigue pendiente por alcance de fase

Cambios manuales externos requeridos:
- ninguno
