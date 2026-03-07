# TASK_013_account_and_device_access_management_minimum

Estado: validated

Fecha de creacion:
2026-03-07

Ultima actualizacion:
2026-03-07

Objetivo:
Hacer operable el gate de acceso de TASK_012 con una superficie minima para consultar y cambiar `accountStatus` y `device accessStatus` sin editar JSON manualmente.

Motivo:
El gate ya existe (`GET /v1/auth/access`), pero faltaba gestion operativa basica para soporte y pruebas reales.

Alcance:
- Backend: endpoints protegidos minimos para listar/buscar cuentas, ver detalle, cambiar `accountStatus` y cambiar `device accessStatus`.
- Web app: extension minima en vista `profile` para operar cuenta/dispositivos manteniendo estilo actual.
- Documentacion viva + historial actualizados, incluyendo correccion de `ARCHITECTURE.md` y `PROJECT_SCOPE.md`.

No tocar:
- Billing/pagos.
- Panel admin completo o portal cliente completo.
- Migracion a DB.
- RBAC complejo.
- Refactor amplio de auth.

Archivos involucrados:
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `docs/01_core/ARCHITECTURE.md`
- `docs/01_core/PROJECT_SCOPE.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_013_account_and_device_access_management_minimum.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Dependencias:
- TASK_012 implementada (gate account/device por `GET /v1/auth/access`).

Implementacion realizada:
- Backend:
  - Nuevo control minimo de operacion por `sub` permitido: `AUTH_OPS_ALLOWED_SUBS` (default `user:demo`).
  - Endpoints nuevos:
    - `GET /v1/auth/ops/accounts?q=&limit=`
    - `GET /v1/auth/ops/accounts/:accountId`
    - `POST /v1/auth/ops/accounts/:accountId/status`
    - `POST /v1/auth/ops/accounts/:accountId/devices/:deviceKey/status`
  - Persistencia JSON mantenida.
  - Auditoria ampliada por actor/timestamp/evento:
    - `account_status_updated`, `device_access_status_updated` (store)
    - `ops_account_status_changed`, `ops_device_status_changed` (endpoint-level)
  - Sin ruptura de contratos existentes de auth/login/QR/OTP/reset.
- Web app:
  - Vista minima de operacion integrada al `mode=profile` existente (sin pagina nueva).
  - Visible solo para usuario operador (`user:*`).
  - Funciones:
    - buscar/listar cuentas
    - ver resumen cuenta + telefonia + TVs vinculadas
    - cambiar `accountStatus` (`trial|active|expired|suspended`)
    - cambiar `accessStatus` de dispositivo (`allowed|blocked`)
  - UI y colores alineados al estilo actual `v34` + `v34-custom`.

Cambios manuales externos:
- Ninguno.
- No requiere cambios en XUI.

Riesgos:
- Seguridad de operacion minima depende de `AUTH_OPS_ALLOWED_SUBS` (sin RBAC completo).
- Persistencia JSON sigue siendo no transaccional para operacion concurrente avanzada.

Pruebas tecnicas ejecutadas:
- `node --check backend/src/server.js` OK
- `node --check backend/src/authPersistence.js` OK
- Smoke test en backend local (puerto 18081):
  - login operador demo
  - listado de cuentas ops
  - detalle de cuenta
  - update de `accountStatus`
  - update de `device accessStatus`
  - respuestas OK y persistencia aplicada

Prueba minima pendiente (usuario):
- `active + allowed` -> entra Home
- cambiar cuenta a `expired` -> siguiente acceso bloqueado
- cambiar cuenta a `suspended` -> acceso bloqueado
- cambiar device a `blocked` -> acceso bloqueado aunque login valido
- revertir a `active + allowed` -> acceso restaurado
- validar que login QR/manual no se rompen

Criterio de exito:
- Validado:
  - se puede operar `accountStatus` y `device accessStatus` sin editar JSON manual.
  - cambios impactan gate actual de TV de forma inmediata en siguiente evaluacion de acceso.
  - web muestra estado y TVs vinculadas con acciones claras.
  - cambios quedan auditados.
