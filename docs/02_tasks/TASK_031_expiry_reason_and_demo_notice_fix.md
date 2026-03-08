# TASK_031_expiry_reason_and_demo_notice_fix

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Evitar que cuentas activas expiradas o sesiones expiradas por token se muestren como `demo expirado` cuando no corresponde.

Alcance:
- Backend access decision:
  - clasificacion de `reasonCode` de expiracion basada en `accountStatusReason`.
  - `DEMO_ALREADY_USED` solo para expiracion por flujo demo.
  - `ACCOUNT_EXPIRED` para expiracion de cuenta activa.
- TV session auto-logout:
  - mensaje por expiracion de token cambia a aviso de sesion expirada.
  - mensaje de demo se conserva solo para trial realmente vencido.

Archivos tocados:
- `backend/src/server.js`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_031_expiry_reason_and_demo_notice_fix.md`
- `docs/03_bugs/BUG_009_wrong_demo_expired_message_for_active_accounts.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Dependencia de `accountStatusReason` para distinguir origen de expiracion; requiere consistencia en transiciones previas del backend.

Pendiente de prueba:
- Cuenta active con expiracion por fecha -> login debe mostrar flujo de cuenta expirada (no demo).
- Cuenta trial vencida -> login debe mostrar demo expirado.
- Sesion token vencida con cuenta aun activa -> aviso de sesion expirada.

Resultado esperado:
- Mensajes y `reasonCode` coherentes con el tipo real de expiracion.

Pasos manuales externos:
- Ninguno.
