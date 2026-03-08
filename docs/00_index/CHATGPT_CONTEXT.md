# CHATGPT_CONTEXT

Fecha: 2026-03-08
Rama: `main`
Tarea activa: `TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry`

## Resumen operativo
- Corregido caso donde TV mostraba cuenta `expired` pero mantenia sesion en Home.
- Ahora la app aplica gate de acceso tambien durante sesion activa, no solo al login.

## Cambios clave recientes
- `AppNavigation`: loop runtime agrega consulta periodica a `/v1/auth/access`.
- Si backend devuelve `canAccessApp=false`, se setea `AuthState.AccessBlocked` con mensaje por reasonCode.
- Polling runtime reducido a 10s para reaccion mas rapida a expiraciones.

## Pruebas tecnicas ejecutadas
- Build Android no ejecutable en este entorno por problema local JBR (`jvm.cfg`).

## Cambios manuales externos
- Ninguno.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry.md`
- `docs/03_bugs/BUG_014_tv_stays_logged_in_after_account_expiry.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
