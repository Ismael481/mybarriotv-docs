# CHATGPT_CONTEXT

Fecha: 2026-03-07
Rama: `main`
Tarea activa: `N/A (TASK_014 validada)`

## Resumen operativo
- TASK_014 introduce rol minimo de cuenta (`customer|operator`).
- Ops authorization pasa a basarse en `role=operator`.
- `AUTH_OPS_ALLOWED_SUBS` queda solo como compatibilidad temporal.
- Web profile muestra bloque ops solo para `operator`.

## Cambios clave recientes
- Backend:
  - cuenta persistida ahora incluye `role` (default `customer`).
  - `/v1/auth/me` incluye role.
  - `/v1/auth/ops/*` valida role operador.
  - auditoria de denegaciones ops (`ops_access_denied`).
  - bootstrap opcional de operadores por `AUTH_BOOTSTRAP_OPERATOR_USERNAMES`.
- Web:
  - visibilidad del panel ops en profile condicionada por `me.user.role`.

## Pruebas tecnicas ejecutadas
- `node --check` OK en backend modificado.
- Smoke tests backend:
  - `/v1/auth/me` devuelve role.
  - acceso ops permitido para operator.
  - acceso ops denegado para token sin rol operador (sin fallback aplicable).
  - operaciones ops siguen impactando gate de acceso en cuenta/dispositivo.
- Validacion operativa en backend activo (2026-03-07):
  - login `customer` => `user.role=customer` y `403` en `/v1/auth/ops/accounts`.
  - login `operator` => `user.role=operator` y acceso correcto a `/v1/auth/ops/accounts`.
  - cambio/reversion de `accountStatus` y `device accessStatus` aplicado correctamente por ops.

## Cambios manuales externos
- Ninguno.
- No hay cambios requeridos en XUI para TASK_014.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_014_minimum_roles_and_ops_authorization.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
