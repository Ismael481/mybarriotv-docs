# TASK_006_authentication_foundation

Fecha: 2026-03-07
Estado: implementada (pendiente validacion funcional en TV fisica)

## Objetivo
Implementar la base minima de autenticacion para habilitar login con JWT antes de introducir logica comercial o control de dispositivos.

## Alcance
### Backend
- Modulo auth basico agregado en `backend/src/auth.js`.
- Endpoints implementados:
  - `POST /v1/auth/login`
  - `GET /v1/auth/me` (protegido JWT)
  - `GET /v1/auth/protected` (protegido JWT, endpoint de prueba)
- Autenticacion simple `username/password` via env (`AUTH_DEMO_USER`, `AUTH_DEMO_PASS`).
- Generacion y verificacion de JWT HS256 sin dependencias externas.
- Middleware de autenticacion aplicado a endpoints protegidos.
- Logs claros para login exitoso, login fallido y accesos protegidos.

### TV App
- Flujo minimo de login conectado a backend.
- Persistencia de token local reutilizando `UserSession` (DataStore existente).
- Envio automatico de `Authorization: Bearer <token>` en requests de red (interceptor ya existente, ahora inyectado en `HttpClient`).
- Bloqueo de acceso a Home cuando no hay sesion activa (guard de navegacion).

## Fuera de alcance (respetado)
- Roles/permisos avanzados.
- Billing, planes, panel admin.
- Device binding/control de dispositivos.
- Cambios de arquitectura grandes.

## Archivos tocados
- `backend/src/auth.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/MainActivity.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt`
- `apps/tv-app/libs/network/src/main/java/com/techlads/network/di/NetworkModule.kt`
- `docs/02_tasks/TASK_006_authentication_foundation.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- JWT actual usa secreto por env; si queda en valor por defecto en ambientes reales, seguridad insuficiente.
- En esta fase, endpoints de contenido bridge (`/v1/content/*`) se mantienen sin proteccion para no romper bridge existente.
- No hay refresh token ni rotacion; sesion minima de fundacion.

## Pendiente de prueba
- Validacion en TV fisica del flujo completo:
  1. App sin sesion inicia en Login.
  2. Login valido navega a Home.
  3. Reinicio de app mantiene sesion local.
  4. Requests hacia backend incluyen header Authorization.
- Prueba de rechazo con credenciales invalidas desde TV app.

## Resultado esperado
- Usuario puede loguearse con `username/password`.
- Backend genera JWT valido.
- `GET /v1/auth/protected` y `GET /v1/auth/me` responden solo con token valido.
- App TV no permite entrar a Home si no hay sesion.

## Validacion ejecutada en sesion
- Backend validado localmente con script Node en puerto `8091`:
  - `POST /v1/auth/login` => `200`
  - `GET /v1/auth/me` con bearer token => `200`
  - `GET /v1/auth/protected` con bearer token => `200`
- Build Android no verificado en este entorno por falta de JBR local (`jvm.cfg` no encontrado).

## Pasos manuales externos
- No se requieren cambios manuales en XUI para esta tarea.
- Configuracion manual requerida solo en backend env:
  - `AUTH_DEMO_USER`
  - `AUTH_DEMO_PASS`
  - `AUTH_JWT_SECRET`
  - `AUTH_TOKEN_TTL_SECONDS`
