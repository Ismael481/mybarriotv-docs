# BUG_008_tv_topbar_profile_label_expiry_and_focus

Estado: closed
Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Media (UX/confusion en home y perfil)

Descripcion:
Se detectaron tres inconsistencias en TV app:
1) topbar mostraba `Perfil: ...` bajo `MyBarrioTV` no requerido,
2) perfil de cuenta mostraba `Vence en 0m` por usar expiracion de token en vez de expiracion real de cuenta,
3) foco inicial del topbar iniciaba sobre avatar/perfil en vez de `Home`.

Causa raiz:
- HomeTopBar renderizaba subtitulo adicional de perfil.
- Modelo de sesion no persistia `account.expiresAt` de backend y UI de perfil usaba `expiresAt` del token.
- TabRow no dirigia foco inicial explicitamente al tab `Home`.

Correccion aplicada:
- Eliminada linea `Perfil: ...` del topbar.
- Agregado `accountExpiresAt` a `AuthState`/`UserSession` y sincronizado desde `/v1/auth/me`.
- Ajustado foco inicial del topbar con `FocusRequester` al tab `Home`.

Archivos:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/topbar/HomeTopBar.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/AuthState.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`

Validacion pendiente:
- Validacion manual en TV fisica/emulador.

