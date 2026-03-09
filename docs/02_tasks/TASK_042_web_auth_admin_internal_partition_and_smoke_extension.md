# TASK_042_web_auth_admin_internal_partition_and_smoke_extension

Estado: completed

Fecha de creacion:
2026-03-09

Ultima actualizacion:
2026-03-09

Objetivo:
Continuar modularizacion incremental del frontend web particionando internamente `login.app.js` y `admin.app.js`, y ampliar smoke contractual ligero sin cambiar producto ni contratos backend.

Alcance:
- Particion interna por responsabilidades de utilidades en auth/admin.
- Mantener UX, reglas de negocio y contratos backend existentes.
- Extender smoke para assets modularizados y rutas ops principales.

Archivos tocados:
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/login-helpers.js`
- `apps/web-app/public/auth/assets/admin-helpers.js`
- `backend/test/minimum-foundation.test.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_042_web_auth_admin_internal_partition_and_smoke_extension.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Web auth:
  - Nuevo modulo `login-helpers.js` con utilidades de estado/validacion/render helper (`normalizePhone`, `isStrongPassword`, `isValidEmail`, `parseJwtExp`, `formatLocalDateTime`, `escapeHtml`, helpers avatar/TV, etc.).
  - `login.app.js` pasa a orquestar flujo y eventos consumiendo helpers externos.
- Web admin:
  - Nuevo modulo `admin-helpers.js` con utilidades de API/render/time formatting (`escapeHtml`, `shortDeviceKey`, `formatDate`, `splitIsoToLocalDateTime`, `toIsoFromDateTimeInputs`, `humanizeDuration`, `statusBadge`).
  - `admin.app.js` pasa a orquestar flujo/eventos consumiendo helpers externos.
- HTML limpio:
  - `login.html` y `admin.html` mantienen markup y cargan assets modularizados por `<script src=...>`.
- Smoke extendido en backend test:
  - verifica referencias/carga de `login-helpers.js` y `admin-helpers.js`
  - verifica rutas ops principales usadas por admin (`list`, `detail`, `status update`, `expiry update`)

Contratos preservados:
- Login/register/device-approve/auth profile/admin ops sin cambios funcionales.
- Endpoints backend auth/ops sin cambios de contrato.

Pruebas ejecutadas:
- `npm test` en `backend/`.
- Resultado: `5` pruebas OK, `0` fallos.

Riesgos:
- `login.app.js` y `admin.app.js` reducen acoplamiento, pero aun conservan tamaño considerable para futuras iteraciones.

Pendiente de prueba:
- Validacion manual de caminos UI completos en navegador real (E2E funcional visual).

Resultado esperado alcanzado:
- Particion interna clara por responsabilidad (helpers modulares + app orquestador).
- HTML limpio y enfocado en markup.
- Smoke/contratos siguen pasando.

Cambios manuales externos:
- Ninguno en XUI.
- Ninguna configuracion externa requerida.
