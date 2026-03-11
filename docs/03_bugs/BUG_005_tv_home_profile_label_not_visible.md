# BUG_005_tv_home_profile_label_not_visible

Estado: closed
Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Media (confusion de contexto de perfil en Home)

Descripcion:
La app TV persistia el perfil seleccionado en sesion, pero en Home no se mostraba de forma explicita el nombre del perfil activo en la cabecera superior, generando percepcion de que la seleccion no se mantenia.

Causa raiz:
`HomeTopBar` solo renderizaba avatar y nombre de app, sin etiqueta textual del perfil activo.

Correccion aplicada:
- `HomeTopBar` ahora recibe `activeProfileName` y pinta `Perfil: <nombre>`.
- `HomeScreenContent` envia `activeProfileName` al topbar.
- Se agrega `active_profile_topbar` a recursos de strings.

Archivos:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/topbar/HomeTopBar.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreenContent.kt`
- `apps/tv-app/app/src/main/res/values/strings.xml`

Validacion pendiente:
- Confirmacion visual en TV fisica/emulador tras reinicio de app.

