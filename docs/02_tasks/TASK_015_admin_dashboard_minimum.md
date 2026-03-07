# TASK_015_admin_dashboard_minimum

Estado: done (pendiente validacion manual final)

Fecha de creacion:
2026-03-07

Ultima actualizacion:
2026-03-07

Objetivo:
Crear una superficie administrativa minima dedicada para operadores, separada del perfil, reutilizando endpoints ops existentes.

Alcance:
- Web app:
  - nueva vista dedicada `/admin` (alias `/ops`).
  - guard de acceso por `role=operator`.
  - listado/busqueda de cuentas.
  - panel de detalle de cuenta y dispositivos.
  - acciones de cambio de `accountStatus` y `device accessStatus`.
  - estados de carga, exito y error visibles.
- Backend:
  - rutas web nuevas para servir dashboard (`GET /admin` y `GET /ops`).
  - sin cambios de logica en gate/auth ni refactor ops.
- Documentacion:
  - actualizacion de indices y changelog.

No tocar:
- Billing/pagos.
- Portal cliente.
- Migracion a DB.
- RBAC complejo.
- Flujos TV app.

Archivos involucrados:
- `backend/src/server.js`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `docs/02_tasks/TASK_015_admin_dashboard_minimum.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se agrego una nueva pantalla dedicada de operaciones en `/admin`:
  - header simple con acciones `Perfil` y `Cerrar sesion`.
  - busqueda/listado de cuentas por `GET /v1/auth/ops/accounts`.
  - detalle de cuenta por `GET /v1/auth/ops/accounts/:accountId`.
  - actualizacion de `accountStatus` por `POST /v1/auth/ops/accounts/:accountId/status`.
  - bloqueo/desbloqueo de dispositivo por `POST /v1/auth/ops/accounts/:accountId/devices/:deviceKey/status`.
- La pagina valida sesion y role con `/v1/auth/me`:
  - `operator`: acceso habilitado.
  - `customer`: acceso denegado y redireccion a perfil.
- Se agrego ruta backend para servir dashboard:
  - `GET /admin`
  - `GET /ops` (alias)
- En perfil (`/auth/login?mode=profile`) se mantiene sesion/dispositivos y se agrega boton `Ir a Admin` solo para operadores.
- Se preserva el estilo actual (`v34` + `v34-custom`) con mejoras de legibilidad para operacion.

Pruebas tecnicas ejecutadas:
- `node --check backend/src/server.js` OK.
- Verificacion de contrato:
  - `/admin` y `/ops` responden archivo HTML de dashboard.
  - dashboard consume endpoints ops existentes sin crear contratos nuevos.
- Smoke test API (2026-03-07):
  - `/admin` -> HTTP 200 y contenido esperado.
  - `/ops` -> HTTP 200 (alias operativo).
  - login `customer` -> `role=customer` y `403` en `/v1/auth/ops/accounts`.
  - login `operator` -> `role=operator` y acceso a `/v1/auth/ops/accounts`.
  - cambio/reversion de `accountStatus` y `device accessStatus` exitoso y persistente.

Pendiente manual de validacion:
- login `operator` -> acceso y operacion completa en `/admin`.
- login `customer` -> acceso denegado en `/admin`.
- busqueda de cuenta + apertura de detalle.
- cambio de `accountStatus` y `device accessStatus` con persistencia tras recarga.
- confirmar no regresiones en perfil/login/QR/gate.
