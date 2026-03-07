# CHATGPT_CONTEXT

Fecha:
2026-03-07

Rama actual:
main

Tarea activa:
TASK_008_qr_device_login_implementation (implementada, pendiente validacion final TV+movil)

Resumen corto:
`TASK_006` cerrada como implementada (auth base JWT + login manual TV).
`TASK_007` cerrada como diseno.
En `TASK_008` se implemento login QR funcional y se agrego extension de vinculacion basica de dispositivo en login.

Estado tecnico actual:
- Backend QR sessions funcionales (`start/status/approve/exchange`).
- Login dual TV funcionando (manual + QR).
- Web minima para aprobar sesion TV funcionando.
- Registro de dispositivo por login exitoso:
  - usa MAC si disponible
  - fallback por prioridad: `serial` -> `widevineId` -> `deviceId` -> `fingerprint`

Consistencia documental:
- `TASK_006` permanece cerrada/implementada.
- `TASK_008` permanece activa hasta validar en TV fisica + movil.

Archivos modificados en TASK_008 (+ extension):
- backend/src/deviceLogin.js
- backend/src/server.js
- backend/.env.example
- backend/README.md
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/DeviceIdentityProvider.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt
- apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt
- apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt
- apps/tv-app/libs/network/src/main/java/com/techlads/network/AuthInterceptor.kt
- apps/web-app/README.md
- docs/02_tasks/TASK_008_qr_device_login_implementation.md
- docs/00_index/ACTIVE_TASK.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/CHATGPT_CONTEXT.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_008_qr_device_login_implementation.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Pendiente de prueba por el usuario:
- Validacion E2E real en TV + movil (escaneo QR, aprobacion web, login automatico TV).
- Confirmar que login manual sigue estable en TV fisica.
- Confirmar que `GET /v1/auth/devices` retorna dispositivo luego del login.

Riesgos o bloqueos:
- Device sessions y dispositivos vinculados en memoria (reinicio backend limpia estado).
- MAC puede no estar disponible en Android moderno; se usa fallback por prioridad.

Cambios manuales externos requeridos:
- XUI: ninguno para TASK_008.
- Configurar backend env para QR:
  - `AUTH_WEB_BASE_URL`
  - `AUTH_DEVICE_SESSION_TTL_SECONDS`
  - `AUTH_DEMO_USER`
  - `AUTH_DEMO_PASS`
  - `AUTH_JWT_SECRET`

Siguiente fase recomendada:
- Persistir vinculacion de dispositivo y sesiones QR en DB, con politicas de revocacion/limites.



