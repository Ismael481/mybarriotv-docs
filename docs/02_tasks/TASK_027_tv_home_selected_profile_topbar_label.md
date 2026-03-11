# TASK_027_tv_home_selected_profile_topbar_label

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Asegurar que, al iniciar la app TV con sesion activa, el perfil seleccionado se conserve en sesion y aparezca visible arriba de Home.

Alcance:
- UI TV Home topbar:
  - mostrar texto del perfil activo (`Perfil: <nombre>`) en la cabecera.
- Flujo de datos:
  - propagar `activeProfileName` hasta `HomeTopBar`.
- I18n:
  - agregar string dedicado para etiqueta de perfil activo.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/topbar/HomeTopBar.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreenContent.kt`
- `apps/tv-app/app/src/main/res/values/strings.xml`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_027_tv_home_selected_profile_topbar_label.md`
- `docs/03_bugs/BUG_005_tv_home_profile_label_not_visible.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Si no existe perfil activo valido, la etiqueta no se mostrara (comportamiento esperado por proteccion de nulos).

Pendiente de prueba:
- Validar en TV fisica/emulador:
  - seleccionar perfil en `WhoIsWatching`.
  - reiniciar app.
  - confirmar que Home muestra el mismo perfil en topbar.

Resultado esperado:
- Usuario ve claramente el perfil activo en cabecera de Home y mantiene continuidad de sesion por perfil.

Pasos manuales externos:
- Ninguno.


