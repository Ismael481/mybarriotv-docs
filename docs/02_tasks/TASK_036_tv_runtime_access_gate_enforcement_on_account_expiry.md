# TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Hacer que la app TV saque automaticamente al usuario de Home cuando la cuenta expira/suspende durante una sesion ya iniciada.

Alcance:
- TV navegacion/runtime auth:
  - consulta periodica de `GET /v1/auth/access` durante sesion activa.
  - si `canAccessApp=false`, transicion a `AuthState.AccessBlocked` con mensaje por `reasonCode`.
  - sincronizacion runtime se mantiene para perfiles/status via `GET /v1/auth/me`.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry.md`
- `docs/03_bugs/BUG_014_tv_stays_logged_in_after_account_expiry.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Mayor frecuencia de polling (`10s`) incrementa levemente llamadas backend por sesion activa.

Pendiente de prueba:
- Dejar TV logueada en Home con expiracion cercana.
- Verificar que al expirar cuenta pase automaticamente a pantalla bloqueada.
- Confirmar mensaje correcto segun `reasonCode` (`ACCOUNT_EXPIRED`, `DEMO_ALREADY_USED`, etc.).

Resultado esperado:
- Sesion TV se bloquea automaticamente cuando backend ya no autoriza acceso.

Pasos manuales externos:
- Ninguno.
