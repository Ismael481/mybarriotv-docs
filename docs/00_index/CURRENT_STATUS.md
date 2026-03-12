# CURRENT_STATUS

## Estado general (2026-03-12)
- `TASK_064_operational_database_foundation`: completed.
- `TASK_063_web_internal_xui_autolink_review_surface`: completed.
- `TASK_062_auto_xui_link_on_account_activation`: completed.
- `TASK_061_ops_xui_link_review_and_actions`: completed.
- Tarea activa actual: ninguna.

## Cierre TASK_064 (2026-03-12)
- Se incorpora fundacion DB operativa minima en backend usando SQLite nativo (`node:sqlite`).
- Nuevo modulo `backend/src/operationalDb.js` con esquema inicial:
  - `operational_accounts`
  - `operational_linked_devices`
  - `operational_events`
- Persistencia en convivencia temporal:
  - JSON (`AUTH_STORE_FILE`) sigue como capa compatible;
  - snapshot operativo se sincroniza a DB (`AUTH_OPERATIONAL_DB_FILE`).
- Lectura de contexto XUI ahora prefiere DB (`source=operational_db`) con fallback seguro a JSON (`source=account_store`).
- Cobertura automatizada ampliada con caso trazable de persistencia en DB para cuenta+estado+link XUI.
- Validacion tecnica: `npm test` backend OK (`16` tests, `0` fallos).

## Estado operativo actual
- Flujo existente de cuenta + auto-link/provision XUI se mantiene estable.
- Superficie web/admin minima de revision/reintento XUI sigue operativa.
- El sistema ya tiene base DB minima para datos operativos criticos y auditoria de eventos.

## Pendientes/riesgos
- Pendiente externo (fuera del repo): rotar y validar `XUI_ADMIN_API_KEY` con permisos reales en XUI.
- Alcance pendiente para futuras tareas: migracion completa de auth/device/otp a DB (fuera de TASK_064).
