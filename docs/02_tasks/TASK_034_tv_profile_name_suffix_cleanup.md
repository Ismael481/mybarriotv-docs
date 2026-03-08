# TASK_034_tv_profile_name_suffix_cleanup

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Quitar el sufijo numerico `1` del nombre mostrado del perfil principal en la pantalla `Settings -> Profile` de la TV app.

Alcance:
- UI TV Profile:
  - normalizar nombre visual para remover solo sufijo final ` 1`.
  - mantener fallback `Perfil activo` cuando el nombre queda vacio.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/settings/screens/profile/ProfileScreen.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_034_tv_profile_name_suffix_cleanup.md`
- `docs/03_bugs/BUG_012_tv_profile_name_shows_primary_suffix.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Si un nombre real del cliente termina intencionalmente en ` 1`, tambien se ocultara en esta vista.

Pendiente de prueba:
- Abrir `Settings -> Profile` con cuenta cuyo nombre de perfil sea `nombre 1` y confirmar render `nombre`.

Resultado esperado:
- UI de perfil muestra solo el nombre base sin numero de perfil principal.

Pasos manuales externos:
- Ninguno.
