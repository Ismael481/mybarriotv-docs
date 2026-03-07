# TASK_010_web_auth_surface_and_registration_prep

Fecha: 2026-03-07  
Estado: implementada (pendiente validacion final del usuario)

## Objetivo
Formalizar la superficie web minima de auth en `apps/web-app` manteniendo backend como API auth central y flujo TV QR sin regresiones.

## Alcance implementado

### Web app
Se creo superficie minima real en `apps/web-app/public/auth/`:
- `login.html` (login web basico)
- `device.html` (aprobacion de sesion TV por QR)
- `register.html` (base visual/funcional de registro, sin OTP real)
- `assets/auth.css` (estilo minimo compartido)
- Refresh visual aplicado: estilo moderno/cinematico con tipografia dedicada, fondo dinamico y componentes consistentes.

### Backend
- Se mantiene contrato auth existente.
- Se elimina pagina HTML embebida en backend y se sirven archivos web desde `apps/web-app/public`.
- Rutas web activas:
  - `GET /auth/device?sessionId=...`
  - `GET /auth/login`
  - `GET /auth/register`
  - `GET /auth/assets/*`
- Se agrega CORS basico configurable:
  - `AUTH_CORS_ORIGIN` (opcional)

### TV app
- Sin cambios de UI ni navegacion.
- Login manual y QR preservados.

## Archivos tocados
- `apps/web-app/public/auth/device.html`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/register.html`
- `apps/web-app/public/auth/assets/auth.css`
- `apps/web-app/README.md`
- `backend/src/server.js`
- `backend/src/deviceLogin.js`
- `backend/.env.example`
- `backend/README.md`
- `docs/02_tasks/TASK_010_web_auth_surface_and_registration_prep.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Como probar en local (TV + movil + web)
1. Levantar backend con:
   - `AUTH_WEB_BASE_URL=http://<IP_LAN_BACKEND>:8080`
2. En TV abrir login y mostrar QR.
3. Escanear QR desde movil:
   - debe abrir `/auth/device?sessionId=...` servido desde `apps/web-app`.
4. Hacer login web y aprobar TV.
5. Confirmar auto-login en TV.
6. Probar rutas web directas:
   - `/auth/login`
   - `/auth/register`

## Fuera de alcance
- OTP SMS real
- perfiles completos
- billing/suscripciones
- panel admin
- portal web final completo

## Listo para siguiente fase OTP
- Base web de registro visible.
- Espacios funcionales para request/verify OTP marcados en UI.
- Backend auth y flujo QR siguen centralizados y operativos.
