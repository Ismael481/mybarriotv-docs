# TASK_032_tv_profile_account_countdown_and_token_ttl_fix

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
En TV app, mostrar estado de cuenta con fecha y contador regresivo real, y corregir expiracion temprana de sesion que expulsaba al login antes del tiempo de cuenta.

Alcance:
- TV UI perfil:
  - mostrar `Estado de cuenta`.
  - mostrar `Vence: dd/MM/yyyy HH:mm`.
  - mostrar `Tiempo restante: ...` en countdown 1s.
- TV data flow:
  - propagar `accountExpiryEpochMs` desde navegacion principal hasta `ProfileScreen`.
- Backend auth:
  - corregir `createAccessToken` para no usar TTL demo en cuentas `account:*`.
- Mensajeria expiracion:
  - mantener distincion entre demo vencido real y sesion expirada por token.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/screens/profile/ProfileScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/SettingsScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/navigation/NestedHomeScreenNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreenContent.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/NestedHomeNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/NestedHomeScreenNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `backend/src/auth.js`
- `backend/src/server.js`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_032_tv_profile_account_countdown_and_token_ttl_fix.md`
- `docs/03_bugs/BUG_010_tv_session_expired_early_due_demo_ttl_token.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Countdown depende de reloj local del dispositivo TV.

Pendiente de prueba:
- Cuenta active con expiracion futura:
  - profile debe mostrar fecha y countdown decreciendo.
  - sesion no debe cerrarse a los 60s.
- Trial vencido y active vencido deben mostrar mensajes correctos de login.

Resultado esperado:
- TV refleja tiempo real de cuenta y evita logout prematuro por configuracion de token.

Pasos manuales externos:
- Ninguno.
