# TASK_035_profile_visibility_and_tv_profile_selection_flow_fix

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Corregir inconsistencia entre web cliente y TV cuando existen dos perfiles en una cuenta:
- web cliente mostrando solo 1 perfil en casos legacy,
- TV entrando directo a Home sin pedir seleccion de perfil.

Alcance:
- TV navegacion/auth:
  - auto-seleccion de perfil solo cuando hay 1 perfil activo.
  - si hay >1 perfil activo y no hay seleccion, abrir `WhoIsWatching`.
- Web perfil cliente:
  - fallback de visibilidad para `p2` basado en perfiles recibidos (`hasP2InPayload`) aunque `profile2Enabled` llegue false por legado.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/web-app/public/auth/login.html`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_035_profile_visibility_and_tv_profile_selection_flow_fix.md`
- `docs/03_bugs/BUG_013_tv_skips_profile_selector_and_web_hides_p2.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Fallback web muestra `p2` si existe en payload aun cuando flag `profile2Enabled` venga desalineado (orientado a corregir legado).

Pendiente de prueba:
- Cuenta con 2 perfiles:
  - Login TV debe abrir selector.
  - `Cambiar perfil` debe regresar a selector.
  - Web cliente debe listar ambos perfiles.

Resultado esperado:
- Comportamiento consistente de perfil entre panel cliente web y app TV.

Pasos manuales externos:
- Ninguno.
