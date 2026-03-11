# TASK_022_account_profile2_entitlement_and_single_demo_enforcement

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Aplicar reglas de negocio para cuentas/perfiles y demo unico por cuenta:
- `p1` obligatorio y activo desde el alta de cuenta.
- `p2` habilitable solo por entitlement de pago (`profile2Enabled`).
- demo por cuenta, no renovable, con bloqueo duro de reactivacion a `trial` cuando ya fue consumido.

Alcance:
- Backend:
  - modelo de cuenta extendido con `profile2Enabled`.
  - normalizacion de store al arranque para garantizar `p1` activo.
  - creacion de perfil limitada a `p2` y solo si `profile2Enabled=true`.
  - `setAccountProfileStatus` endurecido para impedir desactivar `p1`.
  - `GET /v1/auth/me`, `GET /v1/auth/account`, `GET /v1/auth/ops/accounts*` exponen `profile2Enabled`.
  - `POST /v1/auth/ops/accounts/:accountId/status` acepta `profile2Enabled` (boolean).
- Web:
  - perfil cliente oculta totalmente `p2` si no esta habilitado.
  - dashboard admin agrega control de `Segundo perfil pagado` y bloquea `trial/demo` cuando `demoConsumedAt` existe.
- TV app:
  - auto-seleccion de `p1` al iniciar sesion si no hay perfil seleccionado.
  - arranque a Home cuando `p1` activo existe (sin paso obligatorio por selector).

No tocar:
- Integracion billing real.
- Migracion de persistencia a DB.
- XUI.

Archivos tocados:
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/admin.html`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `docs/02_tasks/TASK_022_account_profile2_entitlement_and_single_demo_enforcement.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Cuentas historicas con `p2` previo quedan sujetas a normalizacion de entitlement; requiere validacion manual de casos heredados.
- El repositorio estaba en worktree sucio con cambios previos no relacionados; esta tarea no los revierte.

Pendiente de prueba:
- Cuenta nueva crea `p1` activo automaticamente.
- Cuenta con `profile2Enabled=false` no puede crear/activar `p2` (web + ops).
- Cuenta con demo consumido no puede volver a `trial` desde admin.
- TV inicia con `p1` por defecto sin selector cuando aplica.

Resultado esperado:
- Regla de `p1` obligatorio activo aplicada en backend y reflejada en TV.
- `p2` controlado por entitlement minimo (`profile2Enabled`).
- demo unico por cuenta endurecido en backend + superficie admin.

Pasos manuales externos:
- Ninguno en XUI.
- Sin cambios manuales fuera del repo para completar esta tarea.


