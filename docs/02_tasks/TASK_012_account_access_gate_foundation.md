# TASK_012_account_access_gate_foundation

Estado: done (pendiente validacion final en TV fisica)

Fecha de creacion:
2026-03-07

Ultima actualizacion:
2026-03-07

Objetivo:
Separar autenticacion valida de acceso comercial real en TV: login correcto no implica entrada a Home si cuenta/dispositivo no tienen permiso.

Motivo:
Crear la primera capa minima de control comercial antes de pagos/panel admin/DB, manteniendo arquitectura `App TV -> Backend propio -> XUI`.

Alcance:
- Backend: modelo persistente extendido con `accountStatus` (`trial|active|expired|suspended`) y `device accessStatus` (`allowed|blocked`) manteniendo store JSON.
- Backend: nuevo endpoint protegido `GET /v1/auth/access` con `accountStatus`, `deviceStatus`, `canAccessApp`, `reasonCode` cuando aplica.
- TV app: gate obligatorio post-login manual y post-exchange QR antes de persistir sesion/navegar a Home.
- TV app: nueva pantalla simple `AccessBlocked` con mensaje por `reasonCode`.
- Documentacion viva e historica actualizada y consistente.

No tocar:
- Migracion a DB.
- Pagos.
- Panel admin/portal cliente.
- Refactor amplio de auth.

Archivos involucrados:
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js`
- `backend/src/server.js`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/AccessBlockedScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/Screens.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/MainActivity.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/AuthState.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Dependencias:
- TASK_006 a TASK_011 implementadas (auth base, QR, binding, web auth, OTP).

Implementacion realizada:
- Store auth versionado a `3` con normalizacion retrocompatible de estados.
- Cuenta:
  - nuevo `accountStatus` persistente.
  - helper de actualizacion de estado con auditoria (`account_status_updated`).
- Dispositivo:
  - nuevo `accessStatus` persistente (`allowed|blocked`) manteniendo compatibilidad con `status` legacy (`active|revoked`).
  - helper de actualizacion de estado con auditoria (`device_access_status_updated`).
  - `upsert` de dispositivo preserva bloqueos existentes (no auto-desbloquea por login).
- Backend:
  - nuevo `GET /v1/auth/access` protegido JWT.
  - respuesta: `accountStatus`, `deviceStatus`, `canAccessApp`, `reasonCode` cuando `false`.
  - `reasonCode` implementados: `NO_SUBSCRIPTION`, `ACCOUNT_EXPIRED`, `ACCOUNT_SUSPENDED`, `DEVICE_BLOCKED`.
  - auditoria de consulta de gate (`auth_access_checked`).
  - `POST /v1/auth/login` y `POST /v1/auth/device/exchange` ya no bloquean por dispositivo revocado; autentican y delegan decision comercial al gate.
  - `GET /v1/auth/me` extendido de forma compatible con datos de gate.
- TV app:
  - `BackendAuthApi` agrega `access(accessToken)`.
  - `LoginViewModel` valida gate en login manual y QR exchange antes de `setLoggedIn`.
  - nuevo estado de sesion `AuthState.AccessBlocked` y persistencia asociada en `DefaultUserSession`.
  - nueva ruta/pantalla `AccessBlocked` con mensaje claro y codigo.
  - navegacion central ahora depende de `AuthState` completo (`LoggedOut`, `AccessBlocked`, `LoggedIn`).

Cambios manuales externos:
- Ninguno para esta tarea.
- No requiere cambios en XUI.

Riesgos:
- Compilacion Android local bloqueada por entorno (`jvm.cfg` faltante en JBR local).
- Politica de estados comercial aun minima (sin pagos ni renovacion automatica).
- Persistencia JSON mantiene limitaciones operativas (sin transaccionalidad fuerte).

Pruebas requeridas por el usuario:
- Caso 1: cuenta `active` + dispositivo `allowed` entra a Home.
- Caso 2: cuenta `expired` bloqueada antes de Home.
- Caso 3: cuenta `suspended` bloqueada antes de Home.
- Caso 4: dispositivo `blocked` bloqueado aunque login sea valido.
- Caso 5: login QR aprueba sesion, pero respeta gate de acceso.

Pruebas ejecutadas:
- Sintaxis backend:
  - `node --check backend/src/server.js`
  - `node --check backend/src/authPersistence.js`
  - `node --check backend/src/registrationOtp.js`
- Validacion funcional backend con store temporal `backend/data/auth-store-task012-test.json`:
  - Caso 1: `canAccessApp=true`, `accountStatus=active`, `deviceStatus=allowed`.
  - Caso 2: `canAccessApp=false`, `reasonCode=ACCOUNT_EXPIRED`.
  - Caso 3: `canAccessApp=false`, `reasonCode=ACCOUNT_SUSPENDED`.
  - Caso 4: `canAccessApp=false`, `reasonCode=DEVICE_BLOCKED`.
  - Caso 5 (QR): `device/approve` + `device/exchange` exitosos y luego `access` retorna `DEVICE_BLOCKED`.
- TV:
  - `:app:compileDebugKotlin` no ejecutable en este entorno por error local JBR (`C:\\Program Files\\Android\\Android Studio\\jbr\\lib\\jvm.cfg` no disponible).

Criterio de exito:
- Cumplido a nivel backend + flujo TV implementado:
  - login valido ya no implica Home directo sin gate.
  - acceso depende de cuenta y dispositivo.
  - QR/manual conservados.
  - bloqueo trazable por `reasonCode` y auditoria.

Pendientes:
- Validacion final en TV fisica/LAN del flujo visual `AccessBlocked`.
- Prueba manual de UX/copy final con usuario real en cada `reasonCode`.
