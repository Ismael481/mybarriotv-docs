# TASK_064_operational_database_foundation

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Crear una base de datos operativa minima real para persistir entidades criticas (cuentas, estado operativo, vinculo XUI y eventos) sin romper el flujo actual ni forzar migracion total del backend.

## Alcance
- Auditoria de persistencia actual (JSON/memoria) en backend auth/ops.
- Definicion de fundacion DB minima para operacion.
- Implementacion de conexion y esquema inicial.
- Persistencia operativa minima para cuentas, link XUI, dispositivos vinculados y eventos operativos.
- Lectura minima compatible de contexto XUI desde DB con fallback seguro.
- Convivencia temporal JSON + DB (sin migracion masiva).

## Fuera de alcance
- Billing.
- Migracion total de auth/device/otp a DB.
- Panel admin grande.
- Multi-servidor XUI.
- Refactor amplio o rediseno de arquitectura.
- Cambios grandes en TV app.

## Auditoria resumida previa
- La persistencia principal estaba centralizada en `backend/src/authPersistence.js` usando `AUTH_STORE_FILE` JSON.
- Entidades criticas ya operativas en JSON: `accountsById`, `xuiLink` por cuenta, `linkedDevicesByUser`, `auditEvents`.
- Flujo ops/XUI y auto-link dependian de esa capa JSON como unica base persistente.

## Diseno aplicado (minimo y seguro)
- Tecnologia elegida: SQLite nativo de Node (`node:sqlite`).
- Motivo: no agrega dependencias externas de npm, reduce complejidad operacional para esta fase y permite esquema consultable inmediato.
- Estrategia: convivencia temporal con dual-write.
  - JSON se mantiene para compatibilidad del sistema actual.
  - Snapshot operativo se sincroniza hacia SQLite en carga y en mutaciones.

## Esquema inicial implementado
- `operational_accounts`
  - cuenta operativa, estado, expiracion y link XUI embebido.
- `operational_linked_devices`
  - dispositivos vinculados por cuenta con estado de acceso.
- `operational_events`
  - eventos operativos/auditoria minima.

## Entidades ya persistidas en DB
- Cuenta interna operativa (`account_id`, `username`, `phone`, `role`, `account_status`, `expires_at`, etc.).
- Vinculo XUI por cuenta (`xui_line_id`, `xui_username`, `xui_linked_at`, `xui_linked_by`, `xui_link_reason`).
- Dispositivos vinculados por cuenta (`device_key`, `access_status`, `device_status`, `linked_at`, etc.).
- Eventos operativos clave (mirror de auditoria runtime).

## Compatibilidad mantenida
- `AUTH_STORE_FILE` JSON sigue operativo para no romper contratos actuales.
- `buildResolvedXuiContext` ahora intenta leer link desde DB (`operational_db`) y cae a store JSON (`account_store`) si aplica.
- Endpoints existentes de review/provision/auto-link se mantienen sin cambio de contrato.

## Archivos tocados
- `backend/src/operationalDb.js` (nuevo)
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/.env.example`
- `backend/README.md`
- `docs/04_decisions/ADR_004_operational_db_sqlite_foundation.md` (nuevo)
- `docs/02_tasks/TASK_064_operational_database_foundation.md` (nuevo)
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion ejecutada
- `npm test` en `backend/` OK (`16` tests, `0` fallos).
- Evidencia trazable nueva:
  - test `operational DB persists account status and xui link` verifica caso real donde:
    - cuenta `trial001` pasa a `active`;
    - auto-link XUI queda persistido;
    - SQLite contiene `account_status=active` y `xui_line_id` en `operational_accounts`.

## Riesgos y limites residuales
- `node:sqlite` es feature experimental de Node (advertencia de runtime conocida).
- La migracion total a DB no se hizo en esta tarea (intencional por alcance).
- Persistencia de OTP/sesiones QR y otros dominios sigue en JSON por ahora.

## Pendiente de prueba manual
- Verificar en entorno operativo que el archivo SQLite (`AUTH_OPERATIONAL_DB_FILE`) se actualiza durante operaciones reales de ops/admin y auto-link XUI.

## Pasos manuales si existen
1. Definir en entorno backend:
   - `AUTH_OPERATIONAL_DB_ENABLED=true`
   - `AUTH_OPERATIONAL_DB_FILE` (ruta persistente de entorno).
2. Mantener backup/rotacion de archivos `AUTH_STORE_FILE` y `AUTH_OPERATIONAL_DB_FILE` durante fase de convivencia.

## Cambios externos XUI/config requeridos
- Se mantiene el pendiente externo previo (no nuevo en esta tarea):
  - rotar/validar `XUI_ADMIN_API_KEY`;
  - confirmar permisos reales en XUI para provisioning.

## Estado de cierre formal
- Objetivo de fundacion DB operativa minima cumplido.
- Convivencia JSON + DB implementada sin romper flujo de cuenta + link XUI.
- `TASK_064` cerrada.
