# BUG_010_tv_session_expired_early_due_demo_ttl_token

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (expulsion prematura a login y mensaje de negocio incorrecto)

Descripcion:
La TV expulsaba a login con `Sesion expiro` antes del tiempo de cuenta configurado y, en algunos casos, mostraba texto asociado a demo expirado.

Causa raiz:
- `backend/src/auth.js` generaba JWT para cuentas `account:*` usando `AUTH_ACCOUNT_DEMO_TTL_SECONDS` (default 60s) en vez de TTL normal de sesion.
- Esto hacia que el token venciera mucho antes que la expiracion real de cuenta.

Correccion aplicada:
- `createAccessToken` ahora usa `AUTH_TOKEN_TTL_SECONDS` para todas las sesiones.
- TV profile muestra countdown de cuenta real usando `account.expiresAt`.
- Mensajeria de expiracion demo/sesion diferenciada para reducir confusion.

Archivos:
- `backend/src/auth.js`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/screens/profile/ProfileScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `backend/src/server.js`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`

Validacion pendiente:
- Prueba en TV con cuenta active > 60s para confirmar persistencia de sesion y countdown correcto.
