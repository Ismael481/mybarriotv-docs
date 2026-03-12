# CURRENT_STATUS

## Estado general (2026-03-12)
- `TASK_070_operational_history_and_db_ops_prebilling_foundation`: completed.
- `TASK_069_auth_device_db_transition_closeout`: completed.
- `TASK_067_auth_device_db_read_path_hardening`: completed.
- `TASK_066_auth_device_session_db_foundation`: completed.
- `TASK_065_account_lifecycle_db_hardening`: completed.
- `TASK_064_operational_database_foundation`: completed.
- `TASK_063_web_internal_xui_autolink_review_surface`: completed.
- `TASK_062_auto_xui_link_on_account_activation`: completed.
- `TASK_061_ops_xui_link_review_and_actions`: completed.
- Tarea activa actual: ninguna.

## Cierre TASK_070 (2026-03-12)
- SQLite pasa a sostener tambien operaciones internas prebilling sobre cuentas:
  - listado ops DB-first;
  - filtros operativos;
  - historial de eventos por cuenta;
  - senales minimas de renovacion/expiracion futura.
- `GET /v1/auth/ops/accounts` ahora usa `operational_accounts` como fuente preferente y acepta filtros opcionales:
  - `q`
  - `accountStatus`
  - `role`
  - `status`
  - `hasExpiry`
  - `expiresBeforeDays`
  - `renewalStage`
- `GET /v1/auth/ops/accounts/:accountId` ahora expone `operationalHistory` con `historySource/historyFilter` y acepta `eventType` + `eventLimit`.
- Compatibilidad preservada:
  - sin rutas nuevas grandes;
  - sin billing;
  - fallback a capa historica solo cuando DB no esta disponible.
- Validacion tecnica: `npm test` backend OK (`21` tests, `0` fallos) con nuevo caso trazable DB-first para filtros + historial.

## Cierre TASK_069 (2026-03-12)
- Se cierra documentalmente la transicion operativa auth/device/session.
- SQLite queda declarada como fuente de verdad operativa definitiva para:
  - sesiones QR/device login
  - dispositivos vinculados
  - auditoria auth/device
- `AUTH_STORE_FILE` queda relegado a compatibilidad transitoria, respaldo auxiliar y fallback controlado.
- `backend/README.md` y `.env.example` ahora documentan:
  - rutas persistentes
  - estrategia minima de backup
  - procedimiento minimo de recovery
  - limites conocidos
- Gap detectado: `TASK_068_auth_device_json_retirement_stage_1.md` no existe en `docs/02_tasks/`; se deja documentado como inconsistencia previa, sin inventar alcance faltante.

## Cierre TASK_067 (2026-03-12)
- Las rutas criticas auth/device/session ahora cargan SQLite antes de tocar `AUTH_STORE_FILE`.
- Hardening aplicado en helpers de lectura:
  - sesion QR
  - listado de dispositivos
  - validacion de dispositivos bloqueados/revocados
  - auditoria auth/device
- Hardening aplicado en mutaciones de dispositivos para no depender de que JSON tenga la fila local antes de revocar/bloquear.
- Compatibilidad preservada:
  - login manual
  - login QR
  - revocacion de dispositivos
  - detalle ops/auditoria
- Validacion tecnica: `npm test` backend OK (`20` tests, `0` fallos) con 2 casos nuevos donde DB sostiene lectura/operacion aun con entradas JSON ausentes.

## Cierre TASK_066 (2026-03-12)
- Persistencia DB minima real agregada para auth/device/session reutilizando la base SQLite de `TASK_064`.
- Nuevo esquema operativo:
  - `operational_device_login_sessions`
  - lectura preferente de `operational_linked_devices`
  - lectura preferente de `operational_events`
- `deviceLoginSessions` ahora hace dual-write JSON + DB y puede recuperarse desde SQLite.
- `GET /v1/auth/ops/accounts/:accountId` ahora obtiene `recentAudit` desde DB preferente con fallback JSON.
- Compatibilidad preservada:
  - login manual
  - login QR
  - revocacion de dispositivos
  - TV app
- Validacion tecnica: `npm test` backend OK (`18` tests, `0` fallos) con caso trazable nuevo de sesion QR + linked device + evento auth/device persistidos en DB.

## Cierre TASK_065 (2026-03-12)
- Lifecycle de cuenta endurecido en DB reutilizando la fundacion de `TASK_064`.
- `operational_accounts` ahora persiste metadatos lifecycle:
  - `account_status_updated_at/by/reason`
  - `expiry_updated_at/by/reason`
- Lectura operativa de cuenta (`getAccountById`, `listAccounts`) ahora usa lifecycle preferente desde DB.
- Fallback seguro a JSON cuando DB no esta disponible o esta desfasada respecto al estado actual.
- Validacion tecnica: `npm test` backend OK (`17` tests, `0` fallos) con nuevo caso trazable de status+expiry persistidos en DB y visibles en detalle ops.

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
- El sistema ya tiene base DB minima para datos operativos criticos, read-path auth/device/session endurecido, reglas operativas de backup/recovery documentadas y una base prebilling minima para historial/filtros/eventos/expiracion futura.

## Pendientes/riesgos
- Pendiente externo (fuera del repo): rotar y validar `XUI_ADMIN_API_KEY` con permisos reales en XUI.
- Alcance pendiente para futuras tareas: migracion completa de auth/device/otp a DB, retiro definitivo del store JSON historico y bloque comercial/billing sobre la base prebilling ahora preparada.
