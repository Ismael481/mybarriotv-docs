# TASK_061_ops_xui_link_review_and_actions

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Exponer un flujo ops/admin minimo para revisar el estado de vinculo cuenta interna <-> XUI por `accountId` y operar ese vinculo usando el provisioning ya existente, sin UI admin grande.

## Alcance
- Endpoint ops para revisar estado de vinculo XUI por cuenta.
- Reutilizar accion existente de provisioning/link (`create+link`) como accion controlada.
- Contrato minimo, seguro y trazable para operacion.
- Prueba minima trazable de cuenta vinculada y no vinculada.

## Fuera de alcance
- Billing.
- Panel admin grande/frontend complejo.
- Multi-servidor XUI.
- Refactor amplio.
- Cambios de arquitectura.
- Cambios TV app no relacionados al contrato backend minimo.

## Archivos tocados
- `backend/src/server.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_061_ops_xui_link_review_and_actions.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- Nuevo endpoint ops de revision de link XUI:
  - `GET /v1/auth/ops/accounts/:accountId/xui/link`
  - protegido por JWT + autorizacion ops.
  - respuesta incluye:
    - metadata minima de cuenta (`id`, `username`, `phone`, `accountStatus`, `role`);
    - estado de link (`resolved`, `code`, `xui.lineId`, `xui.username`, `linkedAt`, `linkedBy`, `linkReason`);
    - acciones ops minimas sugeridas (`canProvision`, `canReprovision`, endpoint/metodo de provisioning).
- Reutilizacion de logica existente:
  - usa `buildResolvedXuiContext` para evitar duplicar reglas de vinculacion.
  - accion operativa mantiene `POST /v1/auth/ops/accounts/:accountId/xui/provision` sin crear un flujo paralelo.
- Routing ops actualizado:
  - `backend/src/routes/authOpsRoutes.js` ahora enruta `GET .../xui/link` al nuevo handler.
- Documentacion backend actualizada con endpoint y responsabilidad nueva.

## Contrato minimo agregado
- `GET /v1/auth/ops/accounts/:accountId/xui/link`
  - `200`:
    - `account` (datos minimos ops)
    - `xuiLink` (estado de resolucion y datos de enlace)
    - `actions` (operacion minima sugerida usando provisioning existente)
  - `404`:
    - `code=ACCOUNT_NOT_FOUND`

## Validacion ejecutada
- Suite backend:
  - `npm test` OK (`12` tests, `0` fallos).
- Caso trazable de cuenta con vinculo:
  - test `ops xui link review exposes status and minimal actions` valida `resolved=true` para `active001`.
- Caso trazable de cuenta sin vinculo + accion:
  - mismo test valida `resolved=false` para `trial001`, ejecuta provisioning existente y confirma cambio a `resolved=true`.
- No regresion:
  - test existente `ops xui provision creates and links line idempotently` se mantiene en verde.

## Riesgos residuales
- No hay UI admin en esta tarea (intencional por alcance).
- La accion real de provisioning sigue dependiendo de configuracion externa XUI (Access Code/API key/permisos).

## Resultado esperado
- Operacion puede revisar estado de link XUI por cuenta sin editar store manualmente.
- Operacion puede ejecutar accion controlada de provisioning/link reutilizando el endpoint existente.
- Flujo incremental y auditable sin inflar alcance.

## Estado de cierre formal
- Objetivo y criterio de exito cumplidos con evidencia automatizada trazable.
- `TASK_061` cerrada.

## Pasos manuales si existen
1. Mantener rotacion de `XUI_ADMIN_API_KEY` como proceso externo al repo.
2. Validar en entorno real que operador consulte `GET .../xui/link` y ejecute provisioning cuando `resolved=false`.

## Cambios externos XUI/config requeridos
- Rotacion de `XUI_ADMIN_API_KEY` (fuera del repo).
- Confirmacion de permisos del Access Code/API key para acciones de provisioning.
