# TASK_063_web_internal_xui_autolink_review_surface

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Crear una superficie web/admin minima e interna para revisar el estado de auto-link XUI por cuenta y ejecutar el fallback manual de provisioning/link cuando haga falta, reutilizando endpoints existentes.

## Alcance
- Auditar la superficie web/admin minima actual (`/admin`).
- Integrar una seccion pequena dentro del detalle de cuenta para estado XUI.
- Mostrar estado minimo operativo:
  - `accountStatus` (ya visible en detalle),
  - estado de link XUI (`resolved`/`code`),
  - `xuiLineId` / `xuiUsername`,
  - ultimo resultado de auto-link (`linked|omitted|failed`) a partir de auditoria reciente.
- Permitir reintento manual desde UI usando endpoint existente `POST /v1/auth/ops/accounts/:accountId/xui/provision`.
- Refrescar estado despues de la accion.

## Fuera de alcance
- Billing.
- Panel admin grande/rediseno completo.
- Multi-servidor XUI.
- Refactor amplio frontend/backend.
- Nuevos flujos comerciales.
- Cambios en TV app.
- Replanteo de auth o arquitectura.

## Archivos tocados
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/admin.app.js`
- `apps/web-app/public/auth/assets/admin-api.js`
- `apps/web-app/public/auth/assets/admin-actions.js`
- `apps/web-app/public/auth/assets/admin-render.js`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_063_web_internal_xui_autolink_review_surface.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- UI admin (`/admin`) extendida en el bloque de detalle de cuenta con seccion `XUI Auto-link`:
  - estado de link XUI;
  - identidad enlazada (`xuiLineId` / `xuiUsername`);
  - ultimo estado de auto-link (desde evento `ops_account_xui_auto_link_result` en `recentAudit`);
  - codigo/motivo del ultimo intento;
  - accion de fallback manual.
- API web reutilizada (sin contratos nuevos):
  - lectura: `GET /v1/auth/ops/accounts/:accountId/xui/link`;
  - accion: `POST /v1/auth/ops/accounts/:accountId/xui/provision`.
- Accion operativa agregada en UI:
  - boton `Reintentar link XUI` para ejecutar fallback manual;
  - refresco automatico de detalle/listado tras reintento.
- Mensajes de error amigables extendidos para codigos XUI frecuentes (`XUI_ADMIN_FORBIDDEN`, `XUI_ADMIN_INVALID_BOUQUET_IDS`, etc.).
- Smoke minimo actualizado para validar presencia de elementos nuevos en `admin.html`.

## UI/contratos usados
- Superficie: `apps/web-app/public/auth/admin.html` y modulos `admin-*`.
- Contratos backend reutilizados:
  - `GET /v1/auth/ops/accounts/:accountId` (detalle + `recentAudit`).
  - `GET /v1/auth/ops/accounts/:accountId/xui/link` (estado de link XUI).
  - `POST /v1/auth/ops/accounts/:accountId/xui/provision` (fallback manual).
- No se agregaron endpoints nuevos.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`15` tests, `0` fallos).
- Evidencia minima trazable:
  - UI puede mostrar cuenta enlazada (`resolved=true`) usando `GET .../xui/link`.
  - UI puede ejecutar fallback manual (reintento) y refrescar estado posterior.
  - Smoke automatizado verifica IDs del bloque XUI en HTML admin.

## Riesgos
- Dependencia operativa externa de XUI Admin (`XUI_ADMIN_BASE_URL`, `XUI_ADMIN_API_KEY`, permisos).
- Si XUI no responde o credenciales/permisos fallan, el reintento muestra error y no rompe el resto del panel.

## Pendiente de prueba manual
- Recorrido manual visual en `/admin`:
  1. cuenta ya enlazada (estado visible correcto),
  2. cuenta sin link o con auto-link fallido (reintento y refresco exitoso).

## Resultado esperado
- Operador puede revisar estado de enlace XUI desde web/admin minima.
- Operador identifica rapido si auto-link quedo `linked|omitted|failed`.
- Operador ejecuta fallback manual desde la misma superficie sin herramientas externas.

## Estado de cierre formal
- Objetivo y criterio de exito cumplidos con cambio minimo, interno y auditable.
- `TASK_063` cerrada.

## Pasos manuales si existen
1. Mantener credenciales XUI Admin validas en backend.
2. Si un reintento falla por permisos, corregir Access Code/API key y volver a intentar desde la misma UI.

## Cambios externos XUI/config requeridos
- No se agregan dependencias externas nuevas.
- Se mantiene pendiente externo ya existente: rotacion/validacion de `XUI_ADMIN_API_KEY` y permisos en XUI.
