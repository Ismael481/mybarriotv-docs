# ADR_004_operational_db_sqlite_foundation

Fecha: 2026-03-12

Estado: aceptada

## Contexto
El backend auth/ops operaba con persistencia principal en `AUTH_STORE_FILE` JSON. Para crecer operativamente (cuentas, link XUI, auditoria), era necesario introducir una base de datos minima sin abrir una migracion total ni romper flujos ya validados.

## Decision
Adoptar SQLite nativo (`node:sqlite`) como fundacion DB operativa minima en TASK_064.

Implementacion incremental:
- mantener JSON como capa compatible temporal;
- sincronizar snapshot operativo hacia SQLite (dual-write);
- usar lectura preferente desde SQLite para resolucion de link XUI y fallback a JSON.

## Consecuencias
Positivas:
- base de datos real, consultable y auditable para entidades operativas criticas;
- cambio pequeno y reversible, sin dependencia npm adicional;
- no rompe contratos existentes de ops/web/XUI.

Limitaciones:
- `node:sqlite` aun es experimental en Node;
- coexistencia temporal JSON + DB requiere disciplina de operacion;
- la migracion completa de auth/device/otp queda para tareas futuras.
