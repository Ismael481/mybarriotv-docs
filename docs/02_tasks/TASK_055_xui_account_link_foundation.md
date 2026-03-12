# TASK_055_xui_account_link_foundation

Estado: completed

Fecha:
2026-03-11

Ultima actualizacion:
2026-03-11

## Objetivo
Definir e implementar la base minima para vincular una cuenta interna autenticada de MyBarrioTV con su identidad real en XUI desde backend, sin abrir billing, panel admin nuevo, multi-servidor ni refactors grandes.

## Alcance
- Modelo minimo de vinculo en cuenta: `accountsById.<accountId>.xuiLink`.
- Resolucion backend del contexto XUI para cuenta autenticada via `GET /v1/auth/xui/context` (JWT).
- Prueba automatizada minima para caso vinculado/no vinculado.
- Documentacion de uso para tareas futuras.

## Restricciones
- No implementar billing.
- No abrir panel admin nuevo.
- No mezclar con refactor grande.
- No tocar reproduccion validada salvo verificacion de no-regresion.
- No abrir multi-servidor XUI en esta fase.
- No cambiar arquitectura general `App TV -> Backend -> XUI`.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_054_sincronizacion_indices_post_task_053.md`
- `docs/02_tasks/TASK_055_xui_account_link_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Modelo minimo definido
- Fuente de verdad del vinculo: `AUTH_STORE_FILE` (persistencia auth), dentro de `accountsById.<accountId>.xuiLink`.
- Estructura minima:
  - `xuiLineId` (requerido)
  - `xuiUsername` (opcional)
  - `linkedAt` (opcional)
  - `linkedBy` (opcional)
  - `linkReason` (opcional)
- Resolucion backend:
  - Si existe `xuiLink.xuiLineId`, responde `resolved=true` y `source=account_store`.
  - Si no existe, responde `resolved=false` con `code=XUI_LINK_NOT_CONFIGURED`.

## Riesgos
- En esta fase, la carga del vinculo `xuiLink` es manual en el store; un dato mal cargado resuelve contexto incorrecto.
- El endpoint no consume aun metadata multi-servidor XUI (fuera de alcance actual).

## Pendiente de prueba
- Ninguno bloqueante.
- Validacion manual confirmada por el usuario con resultado satisfactorio y estable en el estado actual del proyecto.

## Criterio de exito
- Existe una forma clara y documentada de resolver identidad XUI por cuenta autenticada.
- Backend obtiene y expone ese contexto XUI de forma controlada (`GET /v1/auth/xui/context` con JWT).
- El bridge actual `App TV -> Backend -> XUI` se mantiene estable.
- Criterio de exito cumplido.

## Prueba minima
1. Ejecutar `npm test` en `backend/` y confirmar caso `auth/xui/context resolves per-account XUI link for authenticated accounts`.
2. Verificar en test:
   - cuenta vinculada -> `resolved=true`, `source=account_store`, `xui.lineId` correcto.
   - cuenta no vinculada -> `resolved=false`, `code=XUI_LINK_NOT_CONFIGURED`.

## Resultado esperado
- Base de vinculacion cuenta interna -> identidad XUI implementada y trazable.
- Contratos existentes de auth/access/playback sin ruptura.
- Tarea cerrada documentalmente por confirmacion del usuario.

## Pasos manuales si existen
1. No aplica para cierre documental final.

## Cambios externos XUI/config requeridos
- No se requiere cambio manual obligatorio en XUI para esta fundacion.
- Si se quiere validar contra linea real, solo se necesita identificar la linea ya existente en XUI (consulta operativa) y reflejar su id en `xuiLink` del store local/backend.
