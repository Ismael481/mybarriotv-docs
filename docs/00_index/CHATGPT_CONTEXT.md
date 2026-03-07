# CHATGPT_CONTEXT

Fecha:
2026-03-07

Rama actual:
main

Tarea activa:
TASK_008_qr_device_login_implementation (implementada, pendiente validacion TV+movil)

Resumen corto:
`TASK_006` queda cerrada como implementada (auth base JWT + login manual TV).
`TASK_007` queda cerrada como diseno.
En `TASK_008` se implemento login QR funcional de dispositivo sin romper login manual:
- backend `loginSession` temporal
- login dual en TV (manual + QR)
- web minima para aprobar sesion
- exchange de token para auto-login en TV

Consistencia documental corregida:
- `TASK_006` se mantiene oficialmente cerrada/implementada.
- `TASK_008` queda como tarea activa.

Archivos modificados en TASK_008:
- backend/src/deviceLogin.js
- backend/src/server.js
- backend/.env.example
- backend/README.md
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt
- apps/web-app/README.md
- apps/tv-app/libs/network/src/main/java/com/techlads/network/AuthInterceptor.kt
- docs/02_tasks/TASK_008_qr_device_login_implementation.md
- docs/00_index/ACTIVE_TASK.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/CHATGPT_CONTEXT.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_006_authentication_foundation.md
- docs/02_tasks/TASK_007_auth_experience_and_device_login_design.md
- docs/02_tasks/TASK_008_qr_device_login_implementation.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Pendiente de prueba por el usuario:
- Validacion E2E real en TV + movil (escaneo QR, aprobacion web, login automatico TV).
- Confirmar que login manual sigue estable en TV fisica.

Riesgos o bloqueos:
- Device sessions en memoria (reinicio backend invalida sesiones pendientes).
- Web minima aun transitoria (servida por backend, no portal web final).

Cambios manuales externos requeridos:
- XUI: ninguno para TASK_008.
- Configurar backend env para QR:
  - `AUTH_WEB_BASE_URL`
  - `AUTH_DEVICE_SESSION_TTL_SECONDS`
  - `AUTH_DEMO_USER`
  - `AUTH_DEMO_PASS`
  - `AUTH_JWT_SECRET`

Siguiente fase recomendada:
- Endurecer persistencia y seguridad de device login, luego migrar la web minima a `apps/web-app` formal y abrir fase OTP real.

