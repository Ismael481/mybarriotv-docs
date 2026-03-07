# CHATGPT_CONTEXT

Fecha: 2026-03-07  
Rama: `main`  
Tarea activa: `TASK_010_web_auth_surface_and_registration_prep`

## Estado corto
- TASK_009 cerrada como implementada/validada (persistencia + hardening auth/device).
- TASK_010 mueve y formaliza la web minima auth a `apps/web-app`.

## Cambios clave TASK_010
- Nuevas paginas en `apps/web-app/public/auth`:
  - `device.html`
  - `login.html`
  - `register.html`
  - `assets/auth.css`
- Refresh visual aplicado sobre esas paginas (sin cambios de contrato backend ni flujo auth).
- Backend sirve esa superficie en:
  - `/auth/device`
  - `/auth/login`
  - `/auth/register`
  - `/auth/assets/*`
- Backend auth API se mantiene igual para TV y web.
- CORS opcional agregado con `AUTH_CORS_ORIGIN`.

## Archivos clave modificados
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

## Cambios manuales externos
- No se requieren cambios en XUI.
- Ajustar env backend para pruebas LAN:
  - `AUTH_WEB_BASE_URL`
  - `AUTH_CORS_ORIGIN` (solo si frontend se sirve en otro origen)

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_010_web_auth_surface_and_registration_prep.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
