# CHATGPT_CONTEXT

Fecha: 2026-03-12
Rama: `main`
Tarea activa: `ninguna`

## Resumen operativo
- `TASK_070`: completed con base prebilling DB-first para historial operativo, filtros y consulta de eventos por cuenta.
- `TASK_069`: completed con cierre operativo/documental de transicion auth/device/session hacia DB.
- `TASK_067`: completed con hardening de rutas de lectura auth/device/session hacia DB preferente.
- `TASK_066`: completed con fundacion DB auth/device/session.
- `TASK_065`: completed con hardening DB de lifecycle de cuenta (estado + expiracion).
- `TASK_064`: completed con fundacion DB operativa minima (SQLite nativo).
- Se mantiene compatibilidad de flujo actual en convivencia temporal JSON + DB.
- `TASK_063`, `TASK_062`, `TASK_061`: completed.

## Estado actual
- Backend ya persiste entidades operativas criticas en `AUTH_OPERATIONAL_DB_FILE`:
  - cuentas operativas y estado,
  - vinculo XUI por cuenta,
  - dispositivos vinculados,
  - eventos operativos,
  - sesiones QR/device login.
- Lifecycle de cuenta (`accountStatus`, `expiresAt`, metadatos de cambio) ya se consulta con preferencia DB y fallback seguro a JSON.
- Auth/device/session ahora usa DB minima real para:
  - `deviceLoginSessions` (dual-write JSON + DB),
  - lectura operativa de `linkedDevicesByUser`,
  - lectura operativa de `auditEvents`.
- Read-path auth/device/session endurecido:
  - sesiones QR, dispositivos vinculados, validacion de revocacion y auditoria auth/device leen DB primero;
  - fallback a JSON queda solo como capa controlada de transicion.
- Fuente de verdad operativa auth/device/session:
  - `AUTH_OPERATIONAL_DB_FILE` / SQLite
  - `AUTH_STORE_FILE` queda solo como compatibilidad transitoria + respaldo auxiliar + fallback controlado
- Operaciones internas prebilling ya apoyadas en DB:
  - `GET /v1/auth/ops/accounts` lista cuentas desde `operational_accounts` preferente con filtros (`accountStatus`, `role`, `status`, `hasExpiry`, `expiresBeforeDays`, `renewalStage`);
  - `GET /v1/auth/ops/accounts/:accountId` consulta `operationalHistory` desde `operational_events` con `eventType` y `eventLimit`;
  - el listado/detalle ops ahora expone senales `expiresInDays`, `isExpired`, `isExpiringSoon`, `renewalStage`.
- `GET /v1/auth/xui/context` y review ops de link mantienen comportamiento actual sin ruptura (DB preferente + fallback JSON).
- No hay tarea activa abierta.

## Cambios manuales externos requeridos
- Rotar `XUI_ADMIN_API_KEY` (externo al repo).
- Confirmar en panel real permisos del Access Code/API key en XUI tras la rotacion.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_070_operational_history_and_db_ops_prebilling_foundation.md`
- `docs/02_tasks/TASK_069_auth_device_db_transition_closeout.md`
- `docs/02_tasks/TASK_067_auth_device_db_read_path_hardening.md`
- `docs/02_tasks/TASK_066_auth_device_session_db_foundation.md`
- `docs/02_tasks/TASK_064_operational_database_foundation.md`
- `docs/02_tasks/TASK_065_account_lifecycle_db_hardening.md`
- `docs/04_decisions/ADR_004_operational_db_sqlite_foundation.md`
- `docs/02_tasks/TASK_063_web_internal_xui_autolink_review_surface.md`
- `docs/02_tasks/TASK_062_auto_xui_link_on_account_activation.md`
- `docs/02_tasks/TASK_061_ops_xui_link_review_and_actions.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
