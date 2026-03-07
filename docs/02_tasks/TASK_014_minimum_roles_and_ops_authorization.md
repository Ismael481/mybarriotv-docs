# TASK_014_minimum_roles_and_ops_authorization

Estado: validated

Fecha de creacion:
2026-03-07

Ultima actualizacion:
2026-03-07

Objetivo:
Reemplazar la autorizacion ops basada en `AUTH_OPS_ALLOWED_SUBS` por un modelo minimo de roles (`customer|operator`) sin RBAC complejo ni DB.

Motivo:
Separar de forma clara cuentas cliente vs cuentas operadoras en operaciones de acceso (`/v1/auth/ops/*`).

Alcance:
- Backend:
  - rol persistente de cuenta (`customer|operator`).
  - `role` incluido en `/v1/auth/me`.
  - `/v1/auth/ops/*` autorizado primariamente por `role=operator`.
  - `AUTH_OPS_ALLOWED_SUBS` queda solo como compatibilidad temporal.
  - auditoria de denegaciones ops y cambios de rol por bootstrap.
- Web:
  - en `mode=profile` mostrar bloque ops solo con `role=operator`.
  - ocultar bloque para `customer`.
- Documentacion:
  - TASK_013 marcada como validada.
  - indices + changelog actualizados.

No tocar:
- Billing/pagos.
- Panel admin completo.
- Portal cliente completo.
- Migracion a DB.
- RBAC complejo.

Archivos involucrados:
- `backend/src/authPersistence.js`
- `backend/src/auth.js`
- `backend/src/server.js`
- `backend/src/registrationOtp.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `docs/02_tasks/TASK_013_account_and_device_access_management_minimum.md`
- `docs/02_tasks/TASK_014_minimum_roles_and_ops_authorization.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Persistencia:
  - modelo de cuenta extendido con `role` (`customer` por default).
  - helper `updateAccountRole()` con auditoria `account_role_updated`.
- Backend auth:
  - JWT incluye `role` (cuando aplica).
  - `/v1/auth/me` devuelve `user.role` y `account.role`.
- Ops authorization:
  - `ensureOpsAuthorized()` valida primero `role=operator`.
  - fallback temporal opcional por `AUTH_OPS_ALLOWED_SUBS`.
  - denegaciones auditadas (`ops_access_denied`).
  - fallback auditado (`ops_access_compat_allowed`).
- Bootstrap operator:
  - nuevo env `AUTH_BOOTSTRAP_OPERATOR_USERNAMES`.
  - al boot promueve cuentas listadas a `operator` y audita evento.
- Web profile:
  - bloque ops visible solo si `me.user.role === "operator"`.
  - para `customer` queda oculto.
  - estilo y layout existentes preservados.

Variables nuevas/relevantes:
- `AUTH_DEMO_ROLE` (`operator` default local).
- `AUTH_BOOTSTRAP_OPERATOR_USERNAMES` (promocion minima por username).
- `AUTH_OPS_ALLOWED_SUBS` (compatibilidad temporal, no mecanismo principal).

Pruebas tecnicas ejecutadas:
- `node --check`:
  - `backend/src/server.js`
  - `backend/src/authPersistence.js`
  - `backend/src/registrationOtp.js`
  - `backend/src/auth.js`
- Smoke tests:
  - `/v1/auth/me` devuelve `role`.
  - acceso `ops` permitido para `operator`.
  - acceso `ops` denegado (`403`) para token sin rol operador (cuando no aplica fallback).
  - cambios ops siguen aplicando al gate (cuenta/dispositivo) sin romper flujo.

Validacion manual/operativa:
- 2026-03-07: login `customer` (`50632133`) devuelve `role=customer` y recibe `403` en `/v1/auth/ops/accounts`.
- 2026-03-07: login `operator` (`demo`) devuelve `role=operator` y accede a `/v1/auth/ops/accounts`.
- 2026-03-07: operador cambia `accountStatus` (`trial -> expired -> trial`) y el cambio persiste.
- 2026-03-07: operador cambia `device accessStatus` (`allowed -> blocked -> allowed`) y el cambio persiste.
- No se observaron regresiones en endpoints auth base durante estas pruebas.
