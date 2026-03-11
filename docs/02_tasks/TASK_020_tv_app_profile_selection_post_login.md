# TASK_020_tv_app_profile_selection_post_login

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Forzar en TV la seleccion de perfil de consumo despues del login de cuenta, mostrando nombre + avatar real de backend antes de entrar a Home.

Alcance:
- TV app:
  - leer perfiles reales de cuenta (`/v1/auth/me`).
  - persistir perfiles y perfil seleccionado en sesion local.
  - redirigir a `WhoIsWatching` cuando el usuario esta logueado y aun no selecciono perfil.
  - usar la pantalla `WhoIsWatching` con perfiles dinamicos (max 2), no hardcodeados.

No tocar:
- Arquitectura global auth.
- Contratos web/admin fuera de lo necesario para TV.
- Integracion XUI.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/wiw/WhoIsWatching.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/wiw/WhoIsWatchingContent.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/AuthState.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/data/AccountProfile.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`

Implementacion realizada:
- Se extendio `AuthState.LoggedIn` para incluir `accountProfiles` y `selectedProfileId`.
- Se agrego modelo `AccountProfile` en `libs/auth`.
- Se extendio `UserSession` con:
  - `setLoggedIn(... accountProfiles, selectedProfileId)`
  - `selectProfile(profileId)`
- `DefaultUserSession` ahora persiste perfiles/seleccion en DataStore y limpia esos datos en logout/bloqueo.
- `BackendAuthApi.me()` ahora acepta token explicito para consultar perfiles inmediatamente tras login.
- `LoginViewModel` ahora consume `/v1/auth/me` con token nuevo y guarda perfiles en sesion.
- `AppNavigation` decide destino post-login:
  - `WhoIsWatching` si hay perfiles activos y no hay perfil seleccionado.
  - `Home` en los demas casos.
- `WhoIsWatching` deja de usar avatares hardcodeados y muestra perfiles reales de sesion (nombre + avatar `boy/girl/old`).
- Al seleccionar perfil en TV, se persiste en sesion y se continua a Home.

Riesgos:
- Si backend responde perfiles vacios o todos `inactive`, TV entra directo a Home (fallback).
- No se pudo ejecutar compilacion Android local por problema de JBR/JDK del entorno (`jvm.cfg` inaccesible).

Pendiente de prueba:
- Login manual y login QR en TV con cuenta que tenga 1 perfil activo.
- Login manual y login QR en TV con cuenta que tenga 2 perfiles activos.
- Verificar que el perfil seleccionado persista si se reinicia app sin cerrar sesion.
- Verificar que al cerrar sesion y volver a iniciar pida nuevamente seleccion de perfil.

Resultado esperado:
- Tras autenticar cuenta en TV, la app solicita perfil antes de Home.
- Se visualizan avatar y nombre reales del perfil.
- No se usa data estatica de perfiles en la pantalla WhoIsWatching.

Pasos manuales externos:
- Ninguno en XUI.
- Si se valida en dispositivo fisico, confirmar backend actualizado con endpoint `/v1/auth/me` activo.


