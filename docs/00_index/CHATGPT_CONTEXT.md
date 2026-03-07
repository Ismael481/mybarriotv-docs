# CHATGPT_CONTEXT

Fecha:
2026-03-07

Rama actual:
main

Tarea activa:
TASK_006_authentication_foundation (implementada, pendiente validacion TV fisica)

Resumen corto:
Se implemento la fundacion minima de autenticacion sin romper el bridge existente.
Backend ahora expone `POST /v1/auth/login`, `GET /v1/auth/me` y `GET /v1/auth/protected` con JWT.
TV app ahora tiene flujo real de login, guarda token localmente y bloquea Home cuando no hay sesion.

Archivos modificados en TASK_006:
- backend/src/auth.js
- backend/src/server.js
- backend/.env.example
- backend/README.md
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/MainActivity.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt
- apps/tv-app/libs/network/src/main/java/com/techlads/network/di/NetworkModule.kt
- docs/02_tasks/TASK_006_authentication_foundation.md
- docs/00_index/ACTIVE_TASK.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/CHATGPT_CONTEXT.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_006_authentication_foundation.md
- docs/02_tasks/TASK_005_bridge_operational_monitoring.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Pendiente de prueba por el usuario:
- Flujo login end-to-end en TV fisica (inicio en login, acceso a Home tras login, persistencia tras reinicio).

Riesgos o bloqueos:
- Falta validacion de build Android en este entorno local (JBR ausente).
- Seguridad auth aun minima (sin refresh/roles/device binding por alcance).

Cambios manuales externos requeridos:
- XUI: ninguno para TASK_006.
- Backend env: definir `AUTH_DEMO_USER`, `AUTH_DEMO_PASS`, `AUTH_JWT_SECRET`, `AUTH_TOKEN_TTL_SECONDS`.

Siguiente fase recomendada:
- Validar TASK_006 en TV fisica y avanzar a base de control de dispositivo/auth hardening.
