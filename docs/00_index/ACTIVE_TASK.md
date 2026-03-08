# ACTIVE_TASK

Tarea activa: **TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry**

Estado actual:
- TV revalida acceso en runtime contra backend (`/v1/auth/access`) durante sesion activa.
- Si la cuenta expira/suspende, la sesion pasa a `AccessBlocked` y sale de Home sin esperar relogin manual.

Objetivo de cierre inmediato:
- Validar en TV fisica/emulador que cuenta vencida dentro de sesion activa salga automaticamente a pantalla bloqueada.

Archivos foco:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `docs/02_tasks/TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry.md`
