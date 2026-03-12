# TASK_065_account_lifecycle_db_hardening

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Endurecer el ciclo de vida operativo de cuenta para que estado y expiracion queden sostenidos de forma consistente por DB, manteniendo compatibilidad con la convivencia temporal JSON + DB.

## Alcance
- Auditoria de manejo actual de `accountStatus`, `expiresAt` y cambios ops/admin.
- Ajuste minimo del esquema operativo en SQLite para lifecycle.
- Persistencia consistente de metadatos de cambio lifecycle en DB.
- Lectura preferente de lifecycle desde DB con fallback seguro a JSON.
- Validacion trazable de cambio de estado + expiracion y reflejo en detalle ops.

## Fuera de alcance
- Billing y renovaciones comerciales.
- Migracion total de auth/device/otp.
- Refactor amplio.
- Panel admin grande/rediseño.
- Cambios grandes en TV app.
- Replanteo arquitectonico.

## Auditoria previa (resumen)
- Los cambios de lifecycle (`updateAccountStatus`, `setAccountExpiry`) ya mutaban JSON y quedaban espejados a DB por snapshot.
- `operational_accounts` no exponia de forma explicita metadatos de cambio lifecycle (`updatedAt/By/Reason`).
- Lectura de cuentas (`getAccountById`, `listAccounts`) seguia usando JSON como fuente principal.

## Implementacion minima aplicada
- `backend/src/operationalDb.js`
  - Se extiende `operational_accounts` con columnas lifecycle:
    - `account_status_updated_at`, `account_status_updated_by`, `account_status_reason`
    - `expiry_updated_at`, `expiry_updated_by`, `expiry_reason`
  - Migracion idempotente de columnas con `PRAGMA table_info + ALTER TABLE` seguro.
  - Nuevo acceso `getOperationalAccountLifecycle(accountId)`.
- `backend/src/authPersistence.js`
  - Lectura de cuenta endurecida con lifecycle preferente desde DB:
    - `getAccountById`
    - `getAccountByUsername`
    - `getAccountByPhone`
    - `listAccounts`
  - Fallback seguro: si DB no existe/no responde o `lastSyncedAt` DB es mas viejo que el estado JSON, se mantiene JSON.
- Compatibilidad preservada:
  - Flujos de auto-link XUI, review/retry web/admin y ops status/expiry no cambian contrato.

## Que parte del lifecycle queda ya sostenida por DB
- Estado de cuenta (`accountStatus`).
- Expiracion (`expiresAt`).
- Timestamps y trazabilidad minima de cambio lifecycle:
  - `accountStatusUpdatedAt/By/Reason`
  - `expiryUpdatedAt/By/Reason`
- Consulta lifecycle preferente para lecturas operativas de cuenta (con fallback controlado).

## Archivos tocados
- `backend/src/operationalDb.js`
- `backend/src/authPersistence.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/02_tasks/TASK_065_account_lifecycle_db_hardening.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion ejecutada
- `npm test` en `backend/`: OK (`17` tests, `0` fallos).
- Evidencia trazable agregada:
  - `account lifecycle status+expiry changes persist in DB and reflect in ops detail`
  - valida:
    1. cambio de estado ops persistido en DB;
    2. cambio de expiracion ops persistido en DB;
    3. detalle de cuenta ops reflejando esos cambios sin romper flujo.

## Riesgos y limites residuales
- `node:sqlite` sigue siendo feature experimental de Node.
- Sigue coexistiendo JSON + DB (intencional); no es migracion total.
- Persistencia completa de dominios OTP/session/device/auth aun fuera de alcance de esta tarea.

## Pendiente de prueba manual
- Validar en entorno operativo que cambios de estado/expiracion hechos desde admin quedan visibles en SQLite y en detalle `/admin` de forma consistente en ejecucion real.

## Pasos manuales si existen
1. Confirmar `AUTH_OPERATIONAL_DB_ENABLED=true`.
2. Confirmar ruta persistente y respaldable para `AUTH_OPERATIONAL_DB_FILE`.

## Cambios externos XUI/config requeridos
- No se agregan nuevas dependencias externas para esta tarea.
- Se mantiene pendiente externo previo: rotacion/validacion de `XUI_ADMIN_API_KEY` y permisos reales en XUI.

## Estado de cierre formal
- Objetivo y criterio de exito de TASK_065 cumplidos.
- `TASK_065` cerrada.
