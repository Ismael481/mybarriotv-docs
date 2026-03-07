# TASK_008_qr_device_login_implementation

Fecha: 2026-03-07
Estado: implementada (pendiente validacion en TV fisica + movil)

## Objetivo
Implementar la primera version funcional de login por QR para TV, manteniendo el login manual existente y una sola arquitectura auth.

## Alcance implementado

### 1) Backend - loginSession QR
Se implemento contrato minimo de sesiones de login de dispositivo:
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`

Comportamiento:
- Crea sesion temporal con `sessionId`, `qrUrl`, expiracion y estado inicial `pending`.
- Estados soportados: `pending`, `approved`, `expired`, `denied`.
- Aprobacion requiere usuario autenticado web (JWT).
- TV obtiene token final por `exchange` cuando la sesion esta `approved`.
- JWT actual se mantiene como auth core.
- Logs claros en start/approve/exchange.

Web minima funcional (servida por backend):
- `GET /auth/device?sessionId=...`
- Si no hay sesion web local, muestra login web simple.
- Si hay sesion web valida, muestra pantalla de aprobacion/denegacion de la sesion TV.
- Aprobacion llama `POST /v1/auth/device/approve`.

### 2) TV App - login dual en misma pantalla
Se mantuvo login manual y se agrego bloque QR visible al mismo tiempo.

Flujo TV QR implementado:
1. LoginViewModel solicita `POST /v1/auth/device/start`.
2. Login muestra QR con `qrUrl`.
3. TV hace polling a `GET /v1/auth/device/status/:sessionId`.
4. Si estado = `approved`, TV llama `POST /v1/auth/device/exchange`.
5. TV guarda token en sesion local y entra a Home automaticamente (por guard actual).

Manejo de errores:
- Si QR expira: mensaje de expiracion y boton `Regenerar QR`.
- Si QR denegado: mensaje y posibilidad de regenerar.
- Si falla polling: error limpio en pantalla.

### 3) Web app / pagina minima
- Se implemento pagina minima funcional para flujo QR.
- En esta fase se sirve desde backend para evitar arquitectura pesada.
- `apps/web-app/README.md` actualizado para reflejar este estado transitorio.

## Fuera de alcance (respetado)
- OTP SMS real.
- Registro completo avanzado.
- Perfiles completos.
- Billing/suscripciones.
- Device binding completo.
- Roles/admin.
- Panel web completo.
- Rediseno amplio de UI.

## Archivos tocados
- `backend/src/deviceLogin.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/data/BackendAuthApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt`
- `apps/web-app/README.md`
- `docs/02_tasks/TASK_008_qr_device_login_implementation.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Endpoints creados
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- `GET /auth/device?sessionId=...` (web minima de aprobacion)

## Flujo completo (paso a paso)
1. TV abre Login y muestra formulario manual + bloque QR.
2. TV crea `loginSession` (`start`) y renderiza QR con URL web propia.
3. Usuario abre URL desde movil y hace login web simple (mismo auth core JWT).
4. Web muestra boton de aprobar TV.
5. Web aprueba sesion (`approve`).
6. TV detecta `approved` en polling (`status`).
7. TV pide token final (`exchange`), guarda sesion y entra automaticamente a Home.

## Como probar en local (TV + movil)

### Backend
1. Configurar en `.env`:
   - `AUTH_DEMO_USER`
   - `AUTH_DEMO_PASS`
   - `AUTH_JWT_SECRET`
   - `AUTH_WEB_BASE_URL` (URL accesible por movil, por ejemplo IP LAN)
2. Levantar backend (`npm start` en `backend/`).

### TV app
1. Asegurar `BACKEND_BASE_URL` apuntando al backend LAN.
2. Abrir app en TV/dispositivo sin sesion previa.
3. Confirmar en login:
   - formulario manual visible
   - QR visible

### Movil/web
1. Escanear QR.
2. Abrir pagina web de aprobacion.
3. Iniciar sesion web con credenciales demo.
4. Aprobar sesion TV.

### Resultado esperado
- TV entra a Home automaticamente tras aprobacion.
- Login manual sigue operativo.

## Validacion ejecutada en sesion
Backend validado por script local en puerto `8092`:
- `start` => `200 pending`
- `status` inicial => `pending`
- `approve` => `approved`
- `status` posterior => `approved`
- `exchange` => `200` con token
- `GET /auth/device` => HTML minima funcional
- Hotfix aplicado: compilacion TV corregida en `apps/tv-app/libs/network/src/main/java/com/techlads/network/AuthInterceptor.kt` (visibilidad del interceptor).

## Riesgos actuales
- Estado de device sessions en memoria (se pierde al reiniciar backend).
- Pagina web minima servida por backend (transitoria, no web app final).
- Falta validacion funcional final en TV fisica + movil real en red local.

## Pendiente para siguiente fase
- Persistencia de `loginSession` (DB) para robustez operativa.
- Migrar pagina minima a `apps/web-app` cuando se abra fase web formal.
- Endurecimiento de seguridad del flujo device login (rate limit, auditoria, anti-abuso).
- Definir/integrar OTP real y modelo completo de cuenta/perfiles (fuera de TASK_008).


