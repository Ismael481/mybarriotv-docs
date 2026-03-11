# BUG_012_tv_profile_name_shows_primary_suffix

Estado: closed
Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Baja (detalle visual/copy)

Descripcion:
En la pantalla de perfil de la TV se mostraba el nombre del cliente con sufijo `1` (perfil principal), por ejemplo `may 1`.

Causa raiz:
La UI renderizaba directamente el `name` del perfil sin normalizar sufijos de slot principal.

Correccion aplicada:
- En `UserDetails` de `ProfileScreen`, se elimina sufijo final ` 1` para presentacion visual.

Archivos:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/screens/profile/ProfileScreen.kt`

Validacion pendiente:
- Verificacion visual en TV fisica/emulador.

