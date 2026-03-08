# BUG_013_tv_skips_profile_selector_and_web_hides_p2

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (flujo de perfil inconsistente entre plataformas)

Descripcion:
Con dos perfiles en la cuenta:
- TV iniciaba directo a Home sin pedir perfil,
- web cliente en algunos casos legacy mostraba solo un perfil.

Causa raiz:
- TV: logica de arranque auto-seleccionaba `p1` por defecto aun en escenarios multi-perfil.
- Web: visibilidad de `p2` dependia estrictamente de `profile2Enabled`, afectando cuentas legacy con flag desalineado.

Correccion aplicada:
- TV: selector obligatorio (`WhoIsWatching`) cuando hay >1 perfil activo y no hay seleccion.
- Web: fallback `hasP2InPayload` para renderizar `p2` cuando viene en respuesta.

Archivos:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/web-app/public/auth/login.html`

Validacion pendiente:
- Prueba manual cruzada (web cliente + TV) en cuenta con dos perfiles.
