# TASK_021_tv_profile_session_context_and_switching

Estado: implemented (pendiente validacion manual en TV)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Convertir el perfil seleccionado en TV en contexto real de sesion, permitiendo visualizar perfil activo, cambiarlo sin logout y recuperarse si el perfil deja de ser valido.

Alcance:
- TV app:
  - mostrar perfil activo en Home de forma discreta.
  - accion `Cambiar perfil` sin cerrar sesion completa.
  - reutilizar `WhoIsWatching` como selector de cambio de perfil.
  - limpiar seleccion invalida cuando el perfil seleccionado ya no existe o pasa a `inactive`.
  - redirigir a `WhoIsWatching` si hay perfiles activos; fallback a `Home` si no hay perfiles activos.
  - mantener persistencia de seleccion entre reinicios de app.
- Backend:
  - reutilizar `GET /v1/auth/me` sin cambios de arquitectura.
  - sincronizacion periodica minima desde TV para detectar cambios de estado de perfil.

No tocar:
- Billing/pagos.
- Migracion a DB.
- RBAC nuevo o panel admin nuevo.
- Refactor grande de auth.
- Cambios grandes en XUI/backend.

Archivos tocados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/MainActivity.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeScreenContent.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/navigation/topbar/HomeTopBar.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/carousel/CarouselItem.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/cast/PersonCard.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/ui/ShimmerAsyncImage.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `backend/src/server.js`

Implementacion realizada:
- UI Home:
  - topbar muestra avatar real del perfil activo en el icono de perfil (entrada a Settings).
- Cambio de perfil sin logout:
  - `Cambiar perfil` en `Settings -> Profile` limpia `selectedProfileId` y fuerza retorno al selector `WhoIsWatching` via enrutado por estado.
- Endurecimiento de seleccion:
  - validacion de `selectedProfileId` ahora exige que el perfil exista y tenga `effectiveStatus=active`.
  - si la seleccion es invalida/inactiva, se limpia automaticamente.
- Sincronizacion con backend:
  - en estado logueado, TV consulta periodicamente `/v1/auth/me` (30s) para refrescar perfiles y detectar cambios hechos desde admin.
  - al refrescar, si perfil seleccionado ya no es valido, se limpia seleccion.
- Persistencia:
  - DataStore conserva `selectedProfileId` cuando sigue valido.
  - al reiniciar app, se restaura contexto de perfil activo si aplica.

Riesgos:
- Polling de `/v1/auth/me` cada 30s agrega carga minima adicional de red.
- No se corrio build Android completa en este entorno por error de JBR/JDK local (`jvm.cfg` inaccesible).

Pendiente de prueba:
- Login con 2 perfiles activos -> seleccionar uno -> entrar Home.
- Cambiar perfil desde `Settings -> Profile` -> volver a Home con perfil nuevo.
- Reiniciar app -> verificar persistencia del perfil seleccionado.
- Desactivar perfil seleccionado desde admin -> TV limpia seleccion y vuelve a selector/fallback.
- Cuenta sin perfiles activos -> fallback a Home.

Resultado esperado:
- La TV muestra perfil activo.
- Se puede cambiar perfil sin cerrar sesion completa.
- Si perfil deja de ser valido, la app se recupera automaticamente.
- No hay regresion en login manual/QR ni access gate.

Pasos manuales externos:
- Ninguno en XUI.
- Para validar reaccion a cambios de estado, usar admin existente para activar/desactivar perfiles.
- Hotfix compilacion: `HomeScreenContentPrev` actualizado para pasar `onSongClick` explicito tras cambio de firma en `HomeScreenContent`.

Actualizacion UX (2026-03-08):
- `Cambiar perfil` se movio del topbar principal a `Settings -> Profile`.
- El icono de perfil en la esquina superior izquierda (entrada normal de Settings) ahora muestra el avatar real del perfil activo.
- Ajuste de branding UI: `app_name` cambiado de `Compose TV` a `MyBarrioTV` para topbar y textos que consumen ese recurso.

Actualizacion UX estado de cuenta (2026-03-08):
- En `Settings -> Profile` se muestra `Estado de cuenta` (Activa/Demo/Expirada/Suspendida).
- Se muestra tiempo restante aproximado para expiracion cuando existe `expiresAt` en sesion.
- `accountStatus` ahora se persiste en sesion TV y se refresca periodicamente desde `/v1/auth/me`.

Hotfix compilacion (2026-03-08):
- `AppNavigation.kt` ahora declara opt-in explicito `@OptIn(ExperimentalTime::class)` en funciones que usan `kotlin.time.Instant`.
- Objetivo: evitar fallo `This declaration needs opt-in` observado en `:app:compileDebugKotlin` en entorno del usuario.

Actualizacion UX carga miniaturas (2026-03-08):
- Se agrego componente reutilizable `ShimmerAsyncImage` para mostrar placeholder animado mientras Coil carga imagen remota.
- Se integro en tarjetas de contenido (`CarouselItem`) y tarjetas de elenco (`PersonCard`).
- Se removio log por item en `CarouselItem` para evitar overhead innecesario durante scroll/carga.

Actualizacion Admin/ops demo-expiracion (2026-03-08):
- `Demo por defecto` en admin ahora se configura por unidad (`minutos|horas`) y se convierte a segundos en backend.
- `GET/POST /v1/auth/ops/settings/demo` extendido para exponer/aceptar valores en segundos y derivadas en minutos/horas.
- Compat de metodos: `/v1/auth/ops/settings/demo` ahora acepta `POST|PUT|PATCH` para evitar `Method Not Allowed` por clientes/proxy.
- Editor de expiracion de cuenta mejorado en admin:
  - selector `datetime-local` + boton `Quitar`.
  - accion rapida `Usar demo global`.
  - accion rapida por duracion (`minutos|horas|dias`).
  - preview de vencimiento y tiempo restante.
- Correccion de consistencia en backend: `handleOpsAccountStatusUpdate` valida `expiresAt` antes de mutar `accountStatus`.
- Endurecimiento de expiracion automatica:
  - cuenta `trial` vencida => consume demo y persiste `accountStatus=expired`.
  - cuenta `active` vencida por fecha => persiste `accountStatus=expired`.
