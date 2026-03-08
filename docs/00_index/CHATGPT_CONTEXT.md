# CHATGPT_CONTEXT

Fecha: 2026-03-08
Rama: `main`
Tarea activa: `TASK_020_tv_app_profile_selection_post_login`

## Resumen operativo
- TV app ahora solicita seleccion de perfil tras login cuando la cuenta tiene perfiles activos.
- `WhoIsWatching` dejo de usar avatares hardcodeados y consume perfiles reales (`name` + `avatar`) desde sesion.
- La sesion auth de TV persiste `accountProfiles` y `selectedProfileId` para controlar navegacion a Home.

## Cambios clave recientes
- Auth TV:
  - `AuthState.LoggedIn` extendido con perfiles/seleccion.
  - `UserSession` extendido con `selectProfile` y persistencia de perfiles en DataStore.
- Login TV:
  - tras login/exchange, se consulta `/v1/auth/me` con token para capturar perfiles.
- Navegacion TV:
  - `LoggedIn` sin perfil seleccionado y con perfiles activos => `WhoIsWatching`.
  - con perfil seleccionado => `Home`.

## Pruebas tecnicas ejecutadas
- Compilacion Android no ejecutable en este entorno por error local JBR:
  - `Error: could not open ...\jbr\lib\jvm.cfg`

## Cambios manuales externos
- Ninguno en XUI.
- Requiere validacion manual en dispositivo TV/emulador con backend actualizado.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_020_tv_app_profile_selection_post_login.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
