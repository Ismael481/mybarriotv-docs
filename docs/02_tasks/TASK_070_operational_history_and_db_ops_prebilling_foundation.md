# TASK_070_operational_history_and_db_ops_prebilling_foundation

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Preparar el siguiente bloque de operaciones importantes del sistema ya apoyadas en DB: historial operativo, filtros internos, consulta de eventos y base minima para renovaciones/expiracion futura, sin abrir billing completo.

## Alcance
- Auditoria de informacion operativa ya disponible en SQLite.
- Implementacion minima para:
  - historial operativo por cuenta;
  - filtros utiles en listado ops;
  - consulta de eventos importantes;
  - senales de expiracion/renovacion futura prebilling.
- Endurecimiento DB-first de lectura operativa en endpoints ops existentes.
- Documentacion de que parte ya queda sostenida por DB.

## Fuera de alcance
- Facturacion completa.
- Pasarela de pagos.
- Logica comercial amplia.
- Multi-servidor.
- Migracion total de cuentas/perfiles a DB.
- Refactor amplio o rediseno de endpoints.

## Auditoria resumida previa
- `operational_accounts`, `operational_linked_devices` y `operational_events` ya existian y sostenian persistencia minima operativa.
- `GET /v1/auth/ops/accounts` seguia listando cuentas desde `listAccounts()` y solo aplicaba filtro `q` en memoria.
- `GET /v1/auth/ops/accounts/:accountId` exponia `recentAudit`, pero no una consulta operativa filtrable de historial ni senales prebilling de expiracion.
- No existia una base minima clara para explotar DB en operaciones previas a billing.

## Implementacion minima aplicada
- `backend/src/operationalDb.js`
  - nuevo helper `listOperationalAccountSnapshots(filters)` sobre `operational_accounts` con conteo agregado de dispositivos y ultimo evento;
  - extension de `listOperationalEvents()` con filtro opcional por `eventTypes`;
  - senales de renovacion calculadas sobre `expiresAt`:
    - `expiresInDays`
    - `isExpired`
    - `isExpiringSoon`
    - `renewalStage`
- `backend/src/server.js`
  - `GET /v1/auth/ops/accounts` ahora usa DB como fuente preferente y acepta filtros opcionales:
    - `q`
    - `accountStatus`
    - `role`
    - `status`
    - `hasExpiry`
    - `expiresBeforeDays`
    - `renewalStage`
  - el listado ops devuelve senales prebilling:
    - `expiresInDays`
    - `isExpired`
    - `isExpiringSoon`
    - `renewalStage`
    - `lastEventAt`
  - `GET /v1/auth/ops/accounts/:accountId` ahora acepta:
    - `eventType` (CSV)
    - `eventLimit`
  - el detalle ops agrega:
    - `operationalHistory`
    - `historySource`
    - `historyFilter`
    - senales de expiracion en `account`
- `backend/src/routes/authOpsRoutes.js`
  - se pasa `url` al handler de detalle ops para soportar filtros de historial sin abrir rutas nuevas.

## Que parte queda ya sostenida por DB
- Listado operativo de cuentas para uso interno prebilling:
  - fuente preferente `operational_accounts`
  - apoyo agregado de `operational_linked_devices` y `operational_events`
- Historial operativo por cuenta:
  - fuente preferente `operational_events`
- Consulta de eventos importantes por tipo:
  - filtro `eventType` sobre `operational_events`
- Senales de renovacion/expiracion futura:
  - calculadas desde `expiresAt` ya sostenido operativamente en `operational_accounts`

## Compatibilidad y fallback
- No se cambian rutas ni contratos base; solo se agregan campos y query params opcionales.
- Si SQLite no esta disponible, el listado ops mantiene fallback a `listAccounts()` y el detalle sigue usando `recentAudit`.
- El detalle de cuenta completo todavia depende de `getAccountById()` y por tanto conserva convivencia temporal JSON + DB para identidad base de cuenta/perfiles.

## Archivos tocados
- `backend/src/operationalDb.js`
- `backend/src/server.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/02_tasks/TASK_070_operational_history_and_db_ops_prebilling_foundation.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos y limites residuales
- `node:sqlite` sigue siendo experimental en Node.
- El detalle completo de cuenta y perfiles no se migro a DB en esta tarea.
- Esta base es prebilling: no introduce cobros, pricing ni reglas comerciales amplias.
- La convivencia JSON + DB sigue activa para dominios fuera del bloque auth/device/session ya cerrado y del detalle completo de cuenta.

## Pendiente de prueba manual
- Validar en entorno ops real los filtros de listado (`renewalStage`, `expiresBeforeDays`) y el historial filtrado por `eventType` sobre una cuenta con eventos reales.

## Resultado esperado
- SQLite deja de ser solo almacenamiento y pasa a soportar mejor operaciones internas previas a billing.
- Operaciones minimas para historial, filtros y renovacion futura quedan trazables y documentadas.
- El siguiente bloque comercial puede apoyarse en estas consultas sin abrir billing completo en esta tarea.

## Cambios externos XUI/config requeridos
- No hay cambios externos nuevos para esta tarea.
- Se mantiene pendiente externo previo: rotacion/validacion de `XUI_ADMIN_API_KEY`.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`21` tests, `0` fallos).
- Caso trazable agregado:
  - `operational ops list and account history use DB-backed filters and prebilling signals`
  - valida:
    1. listado ops resuelto desde DB con filtros;
    2. senales de expiracion/renovacion;
    3. historial de cuenta filtrado por tipo de evento desde DB.

## Estado de cierre formal
- Objetivo y criterio de exito de `TASK_070` cumplidos.
- `TASK_070_operational_history_and_db_ops_prebilling_foundation` cerrada.
