# CHATGPT_CONTEXT

Fecha: 2026-03-07
Rama: `main`
Tarea activa: `TASK_015_admin_dashboard_minimum`

## Resumen operativo
- TASK_015 agrega dashboard admin minimo dedicado en `/admin` (alias `/ops`).
- Acceso a dashboard permitido solo para `role=operator`.
- Perfil se mantiene operativo y ahora actua como puerta hacia admin para operadores.

## Cambios clave recientes
- Backend:
  - nuevas rutas web `GET /admin` y `GET /ops` para servir dashboard.
  - se reutilizan endpoints ops existentes sin cambios de logica.
- Web:
  - nueva pantalla `auth/admin.html` con busqueda, detalle y acciones ops.
  - guard de role desde frontend por `/v1/auth/me` (`operator` requerido).
  - `profile` agrega boton `Ir a Admin` solo para operadores.
  - estilos admin incorporados en `v34-custom.css` preservando visual actual.

## Pruebas tecnicas ejecutadas
- `node --check backend/src/server.js` OK.
- Verificacion de contrato/rutas:
  - `/admin` y `/ops` sirven dashboard web.
  - dashboard usa `GET/POST /v1/auth/ops/*` existentes.
- Smoke tests en backend activo:
  - `/admin` HTTP 200 con contenido de dashboard.
  - `customer` recibe `403` en `/v1/auth/ops/accounts`.
  - `operator` accede a `/v1/auth/ops/accounts`.
  - cambio/reversion de estado de cuenta y dispositivo verificado.

## Cambios manuales externos
- Ninguno.
- No hay cambios requeridos en XUI para TASK_015.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_015_admin_dashboard_minimum.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
