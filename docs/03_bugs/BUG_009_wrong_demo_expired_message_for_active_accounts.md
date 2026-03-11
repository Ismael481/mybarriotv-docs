# BUG_009_wrong_demo_expired_message_for_active_accounts

Estado: closed
Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (mensaje incorrecto de negocio y cierre de sesion confuso)

Descripcion:
Cuentas activas con tiempo agregado estaban mostrando en login `Tu demo ha expirado...` cuando expiraba sesion/token o vencian por fecha de cuenta, aun habiendo consumido demo anteriormente.

Causa raiz:
- Backend clasificaba expiracion como `DEMO_ALREADY_USED` solo por presencia de `demoConsumedAt`, incluso para cuentas activas expiradas.
- TV auto-logout por expiracion de token mostraba siempre aviso de demo vencido.

Correccion aplicada:
- Backend: `buildAccessDecision` usa `accountStatusReason` para distinguir expiracion por demo (`DEMO_ALREADY_USED`) de expiracion de cuenta activa (`ACCOUNT_EXPIRED`).
- TV: `scheduleAutoLogout` muestra aviso de sesion expirada por token y reserva aviso de demo para trial realmente vencido.

Archivos:
- `backend/src/server.js`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`

Validacion pendiente:
- Pruebas manuales con cuenta trial vencida y cuenta active vencida por fecha.

