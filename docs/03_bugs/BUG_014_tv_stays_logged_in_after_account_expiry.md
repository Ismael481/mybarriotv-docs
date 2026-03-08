# BUG_014_tv_stays_logged_in_after_account_expiry

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (violacion de gate de acceso en runtime)

Descripcion:
La TV podia permanecer en Home aun cuando backend/admin ya reflejaban cuenta expirada y perfil mostraba estado `expired`.

Causa raiz:
El gate de acceso (`/v1/auth/access`) se aplicaba en login/exchange, pero no durante sesion activa continua.

Correccion aplicada:
- `AppNavigation` agrega verificacion periodica de `/v1/auth/access`.
- Cuando `canAccessApp=false`, la sesion cambia a `AccessBlocked` automaticamente.

Archivos:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`

Validacion pendiente:
- Prueba manual en TV fisica/emulador con expiracion en caliente.
