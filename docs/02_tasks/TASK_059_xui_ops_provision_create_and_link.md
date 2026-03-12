# TASK_059_xui_ops_provision_create_and_link

Estado: completed

Fecha de creacion:
2026-03-11

Ultima actualizacion:
2026-03-11

## Objetivo
Implementar Fase 1 de provisioning controlado: endpoint ops backend que cree una linea en XUI Admin API y guarde automaticamente `xuiLink` en la cuenta interna objetivo.

## Alcance
- Backend:
  - nuevo cliente Admin API XUI desacoplado.
  - nuevo endpoint ops protegido para `create+link`.
  - persistencia tipada de `xuiLink` en account store.
- Pruebas:
  - caso automatizado de provisioning + idempotencia.
- Documentacion:
  - actualizar README/env e indices obligatorios.

## Fuera de alcance
- Auto-provision dentro de `register/complete`.
- Cambios de arquitectura global.
- Billing y multi-servidor XUI.

## Archivos tocados
- `backend/src/xuiAdminClient.js`
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/src/routes/authOpsRoutes.js`
- `backend/test/minimum-foundation.test.js`
- `backend/.env.example`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_058_xui_admin_api_discovery_for_auto_line_provisioning.md`
- `docs/02_tasks/TASK_059_xui_ops_provision_create_and_link.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- Nuevo modulo `xuiAdminClient`:
  - `buildXuiAdminConfig`
  - `createXuiLine`
  - manejo de timeout/redirect/no-JSON/errores de accion.
- Persistencia:
  - nuevo helper `updateAccountXuiLink` en `authPersistence`.
- Endpoint nuevo:
  - `POST /v1/auth/ops/accounts/:accountId/xui/provision`
  - protegido por JWT + autorizacion ops.
  - comportamiento:
    - idempotente basico: si cuenta ya tiene `xuiLink`, responde `alreadyLinked=true` sin crear de nuevo (salvo `forceProvision=true`).
    - crea linea en XUI (`create_line`) con username/password y params extras opcionales.
    - persiste `xuiLink` automaticamente en store local.
- Parametros de request soportados:
  - `xuiUsername` / `username`
  - `xuiPassword` / `password`
  - `maxConnections`
  - `expDate`
  - `bouquetIds` (mapeado a `bouquets_selected[]`)
  - `xuiCreateParams` (objeto passthrough para variantes de panel)
  - `includeCredentials` (opcional, incluye password de provision en respuesta)
  - `forceProvision` (opcional)

## Pruebas automatizadas ejecutadas
- `npm test` en `backend/`: OK (`9` tests, `0` fallos).
- Nuevo test:
  - `ops xui provision creates and links line idempotently`
  - usa servidor fake de Admin API para validar `create+link` y no duplicar en segundo llamado.

## Pruebas manuales ejecutadas (runtime real)
- Validacion con operador (`demo`) y cuenta objetivo `Isma`:
  - llamada inicial sin `forceProvision` sobre cuenta ya enlazada: `alreadyLinked=true` (idempotencia OK).
  - llamada con `forceProvision=true`: `createdInXui=true`, `lineId=5`, `xuiUsername=isma_auto_3507`.
  - validacion posterior `GET /v1/auth/xui/context` con login de `Isma`:
    - `resolved=true`
    - `xui.lineId=5`
    - `xui.username=isma_auto_3507`
- Comportamiento esperado en reprovision repetido:
  - si se reutiliza mismo username, XUI puede devolver `XUI_ADMIN_ACTION_FAILED` por duplicado (esperado por restriccion de panel).

## Riesgos
- Diferencias entre variantes XUI:
  - campo de bouquet puede variar por panel (`bouquets_selected[]`, `bouquet`, etc.).
  - se mitiga con `xuiCreateParams` passthrough.
- Seguridad:
  - `api_key` via query string requiere sanitizacion/redaction en logs externos.

## Pendiente de prueba manual
- Validacion operativa ampliada (opcional) con mas bouquets reales y casos de cuentas adicionales.

## Resultado esperado
- Operacion deja de editar `auth-store.json` manualmente para linkear cuenta.
- Operador puede provisionar linea y enlazar cuenta desde un solo endpoint backend auditable.

## Cierre
- Objetivo cumplido en backend + pruebas + validacion manual minima real.

## Pasos manuales si existen
1. Configurar env backend:
   - `XUI_ADMIN_BASE_URL=http://panel.mybarriotv.com/lHpqPGtQ/`
   - `XUI_ADMIN_API_KEY=<api_password_del_usuario_api>`
2. Reiniciar backend.
3. Ejecutar:
   - login operador (`/v1/auth/login`)
   - `POST /v1/auth/ops/accounts/<accountId>/xui/provision`
   - `GET /v1/auth/xui/context` con token de la cuenta objetivo.

## Cambios externos XUI/config requeridos
- Access Code API habilitado en panel (`lHpqPGtQ` validado en runtime).
- API key valida (campo `API Password` del usuario con permisos).
- Grupos del Access Code configurados para el usuario operador/reseller correspondiente.
