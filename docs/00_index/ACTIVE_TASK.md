# ACTIVE_TASK

Tarea activa: **TASK_020_tv_app_profile_selection_post_login**

Estado actual:
- Implementacion de seleccion de perfil post-login completada en TV app.
- Documentacion base y de tarea actualizadas.
- Pendiente validacion manual en TV fisica/emulador.

Objetivo de cierre inmediato:
- Validar que login (manual y QR) redirija a `WhoIsWatching` cuando haya perfiles activos.
- Validar que seleccion de perfil persista y permita entrada a Home.
- Validar casos de 1 y 2 perfiles.

Archivos foco:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/wiw/WhoIsWatching.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/wiw/WhoIsWatchingContent.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
