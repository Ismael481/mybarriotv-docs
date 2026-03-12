# TASK_062_auto_xui_link_on_account_activation

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Automatizar el enlace/provisioning XUI desde backend en el punto mas natural y seguro del flujo actual de negocio, dejando la operacion manual como respaldo y no como camino principal.

## Alcance
- Auditar el flujo real actual de cuenta para escoger punto de integracion minimo.
- Integrar auto-link en `POST /v1/auth/ops/accounts/:accountId/status` cuando `accountStatus=active`.
- Reutilizar integralmente la logica existente de provisioning `create+link`.
- No duplicar ni reprovisionar cuando la cuenta ya tiene `xuiLink`.
- Mantener trazabilidad minima de resultado (`linked|omitted|failed`) y fallback manual disponible.

## Fuera de alcance
- Billing.
- Panel admin grande.
- Multi-servidor XUI.
- Refactor amplio.
- Automatizaciones comerciales complejas.
- Cambios grandes en TV app.
- Replanteo de arquitectura.

## Punto de integracion elegido (auditoria)
- Punto seleccionado: cambio ops de estado de cuenta a `active` en `handleOpsAccountStatusUpdate` (`backend/src/server.js`).
- Motivo: es el punto mas pequeno y seguro ya existente para activacion/aprobacion operativa sin introducir flujos nuevos ni exponer XUI al cliente.
- Puntos descartados en esta tarea:
  - `register/complete`: crea cuenta en `trial` y no representa aprobacion/activacion operativa final.
  - Flujos cliente TV/web: fuera de alcance y no deben depender de interaccion directa con XUI.

## Archivos tocados
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_062_auto_xui_link_on_account_activation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- Se extrajo helper interno reutilizable `executeAccountXuiProvision(...)` para centralizar la logica existente de `create+link`.
- `POST /v1/auth/ops/accounts/:accountId/xui/provision` sigue operativo, ahora consumiendo el helper interno (sin cambio de contrato funcional principal).
- En `POST /v1/auth/ops/accounts/:accountId/status`:
  - cuando `accountStatus=active`, se intenta auto-link XUI automaticamente;
  - si ya existe link, no reprovisiona y marca resultado `omitted`;
  - si auto-link falla, el endpoint de estado mantiene `200` y reporta `xuiAutoLink.status=failed`.
- Se agrego trazabilidad minima:
  - respuesta de `status` ahora incluye `xuiAutoLink` con `attempted`, `status`, `reason` y fallback;
  - auditoria `ops_account_xui_auto_link_result` con resultado y codigo.
- La operacion manual queda disponible como respaldo:
  - `POST /v1/auth/ops/accounts/:accountId/xui/provision`.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`15` tests, `0` fallos).
- Evidencia minima trazable (automatizada):
  - `ops account status active triggers auto xui link for unlinked account`.
  - `ops account status active auto-link omits provisioning when account already linked`.
  - `ops account status active keeps flow when auto-link fails`.

## Riesgos
- Dependencia externa de configuracion XUI Admin (`XUI_ADMIN_BASE_URL`, `XUI_ADMIN_API_KEY`, permisos).
- Si XUI falla o credenciales son invalidas, el auto-link queda en `failed` y requiere operacion manual de respaldo.

## Pendiente de prueba manual
- Validar en entorno real un caso ops de activacion con panel XUI productivo y confirmar trazabilidad operativa end-to-end.

## Resultado esperado
- Existe punto claro de auto-link en activacion (`accountStatus=active`).
- Cuenta operativa puede quedar enlazada sin accion manual del cliente.
- Si ya existe link, no hay duplicacion.
- Si falla auto-link, falla controlada con trazabilidad y fallback manual intacto.

## Estado de cierre formal
- Objetivo y criterio de exito cumplidos con cambio minimo, reversible y auditable.
- `TASK_062` cerrada.

## Pasos manuales si existen
1. Mantener `XUI_ADMIN_BASE_URL` y `XUI_ADMIN_API_KEY` validos en entorno backend.
2. Si `xuiAutoLink.status=failed`, ejecutar fallback ops:
   - `POST /v1/auth/ops/accounts/:accountId/xui/provision`.

## Cambios externos XUI/config requeridos
- No se introduce nueva dependencia externa.
- Se mantienen los pasos externos ya existentes:
  - rotacion de `XUI_ADMIN_API_KEY`;
  - confirmacion de permisos Access Code/API key en panel XUI.
