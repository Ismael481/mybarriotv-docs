# TASK_030_tv_topbar_profile_label_expiry_and_initial_focus_fix

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Corregir tres puntos en TV app:
1) remover texto `Perfil: ...` debajo de `MyBarrioTV` en Home,
2) mostrar vencimiento real de cuenta en perfil usando datos de backend,
3) mover foco inicial del topbar a `Home` al iniciar app.

Alcance:
- UI Home topbar:
  - eliminada linea secundaria de perfil bajo branding.
  - foco inicial dirigido a tab `Home`.
- Sesion/auth model:
  - agregado `accountExpiresAt` al estado auth persistido.
  - sincronizacion desde `/v1/auth/me` (`account.expiresAt`).
- Perfil cuenta TV:
  - calculo de vencimiento basado en `accountExpiresAt`.
  - mitigacion de texto `Vence en 0m` por redondeo de menos de 1 minuto.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/topbar/HomeTopBar.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/AuthState.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `apps/tv-app/app/src/main/res/values/strings.xml`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_030_tv_topbar_profile_label_expiry_and_initial_focus_fix.md`
- `docs/03_bugs/BUG_008_tv_topbar_profile_label_expiry_and_focus.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Ajustes de foco en TV pueden variar por dispositivo/control remoto; requiere validacion en hardware real.

Pendiente de prueba:
- Inicio de app: foco inicial visible sobre `Home`.
- Home topbar: sin `Perfil: ...` bajo logo.
- `Settings -> Profile`: vencimiento muestra tiempo real de cuenta (cuando backend envie `account.expiresAt`).

Resultado esperado:
- UX consistente en Home y estado de cuenta correcto en perfil.

Pasos manuales externos:
- Ninguno.


