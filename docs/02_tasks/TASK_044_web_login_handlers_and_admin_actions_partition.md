# TASK_044_web_login_handlers_and_admin_actions_partition

Estado: completed

Fecha de creacion:
2026-03-09

Ultima actualizacion:
2026-03-09

Objetivo:
Completar la particion fina del frontend web auth/admin separando handlers/eventos de `login.app.js` por dominio y moviendo acciones de `admin.app.js` a modulo `admin-actions`, sin cambiar producto ni contratos backend.

Alcance:
- Web login: handlers/eventos por dominio (otp, profile, ops-lite).
- Web admin: acciones operativas extraidas a `admin-actions.js`.
- Mantener UX y contratos existentes.

Archivos tocados:
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/login-handlers.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/admin-actions.js`
- `backend/test/minimum-foundation.test.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_044_web_login_handlers_and_admin_actions_partition.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Login web:
  - Nuevo modulo `login-handlers.js` con factories por dominio:
    - `createOtpHandlers`
    - `createProfileHandlers`
    - `createOpsLiteHandlers`
  - `login.app.js` queda orientado a wiring/orquestacion y reutiliza handlers externos.
  - `login.html` carga nuevo asset `login-handlers.js`.
- Admin web:
  - Nuevo modulo `admin-actions.js` con acciones operativas encapsuladas:
    - actualizar estado de cuenta
    - actualizar expiracion
    - actualizar estado de dispositivo
    - actualizar estado de perfil
    - crear segundo perfil
    - eliminar cuenta
  - `admin.app.js` delega estas acciones y queda mas pequeno.
  - `admin.html` carga nuevo asset `admin-actions.js`.
- Smoke:
  - Extendida validacion de carga de assets:
    - `/auth/assets/login-handlers.js`
    - `/auth/assets/admin-actions.js`

Contratos preservados:
- Sin cambios de reglas de negocio.
- Sin cambios incompatibles en rutas/JSON backend.

Pruebas ejecutadas:
- `npm test` en `backend/`.
- Resultado: `5` pruebas OK, `0` fallos.

Riesgos:
- Modularizacion interna mejora mantenibilidad; cobertura UI sigue basada en smoke, sin E2E navegador completo.

Pendiente de prueba:
- Validacion manual visual completa de `/auth/login` y `/admin` en navegador real.

Resultado esperado alcanzado:
- `login.app.js` mas pequeno y dividido por handlers de dominio.
- `admin.app.js` mas pequeno al mover acciones a `admin-actions.js`.
- `npm test` en verde y sin regresiones detectadas.
- Documentacion e indices sincronizados al estado real.

Cambios manuales externos:
- Ninguno en XUI.
- Ninguna configuracion externa requerida.

Hotfix UI/codificacion (2026-03-09):
- Corregidos caracteres mojibake en render de perfiles web (`lapiz` y separador intermedio).
- Mensajes de `Contrasena` normalizados para evitar textos corruptos en pantalla.