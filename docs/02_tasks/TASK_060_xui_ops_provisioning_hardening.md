# TASK_060_xui_ops_provisioning_hardening

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Endurecer de forma minima y controlada el flujo de provisioning `create+link` con XUI ya existente, sin cambiar la arquitectura general del proyecto.

## Alcance
- Revisar compatibilidad real con bouquets/paquetes usados en operacion.
- Mejorar manejo de diferencias de parametros entre variantes de panel XUI.
- Mejorar mapeo de errores operativos del flujo de provisioning.
- Documentar el pendiente de rotacion de credenciales/API key expuestas en pruebas.

## Fuera de alcance
- Billing.
- Panel admin grande.
- Multi-servidor XUI.
- Refactor amplio.
- Nuevas features de TV app ajenas a provisioning XUI.
- Cambios de arquitectura global.

## Archivos tocados
- `backend/src/xuiAdminClient.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `backend/.env.example`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_060_xui_ops_provisioning_hardening.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada
- Cliente XUI Admin (`xuiAdminClient`):
  - nueva accion `get_bouquets` (`listXuiBouquets`).
  - clasificacion explicita de permisos insuficientes/API key invalida a `XUI_ADMIN_FORBIDDEN` (`HTTP 403`).
- Handler provisioning (`handleOpsAccountXuiProvision`):
  - validacion defensiva de bouquets previa a `create_line`.
  - nuevo control por `XUI_ADMIN_BOUQUET_VALIDATION_MODE`:
    - `enforce` (default): bloquea provisioning si falla validacion o hay bouquets invalidos.
    - `warn`: permite continuar y deja warning de validacion.
    - `off`: desactiva validacion previa.
  - respuesta `400` con `XUI_ADMIN_INVALID_BOUQUET_IDS` cuando hay ids no validos.
  - respuesta enriquecida con `bouquetValidation` cuando aplica.
- Pruebas automatizadas:
  - nuevo test de rechazo por bouquet invalido (sin llegar a `create_line`).
  - nuevo test de permisos insuficientes/API key invalida mapeado a `403`.

## Validacion ejecutada
- `npm test` en `backend/`: OK (`11` tests, `0` fallos).
- Evidencia minima de hardening (automatizada y trazable):
  - provisioning rechaza `bouquetIds` invalidos con `XUI_ADMIN_INVALID_BOUQUET_IDS`.
  - provisioning mapea `INVALID_API_KEY` a `XUI_ADMIN_FORBIDDEN` (`403`).
- Evidencia de no regresion:
  - el test existente `ops xui provision creates and links line idempotently` permanece en verde.

## Caso de permisos insuficientes
- Quedo probado en entorno controlado de pruebas (fake XUI Admin) sin tocar credenciales reales del panel productivo.
- Para panel real, la validacion final queda como paso operativo externo posterior a rotacion de API key.

## Riesgos residuales
- Algunas variantes de panel pueden exponer `get_bouquets` con formato no estandar.
- En esos casos, usar `XUI_ADMIN_BOUQUET_VALIDATION_MODE=warn` (o `off` de forma excepcional y documentada) hasta ajustar mapping.

## Resultado esperado
- Provisioning mas robusto frente a bouquets invalidos.
- Error de permisos insuficientes mejor clasificado para operacion.
- Flujo `create+link` existente preservado.

## Estado de cierre formal
- Objetivo de la iteracion cumplido.
- Criterio de exito cumplido con evidencia automatizada y trazable.
- `TASK_060` cerrada.

## Pasos manuales si existen
1. Rotar `XUI_ADMIN_API_KEY` en XUI y actualizar entorno backend.
2. Validar en panel real, con la API key rotada, un caso de provisioning exitoso y uno denegado por permisos.

## Cambios externos XUI/config requeridos
- Rotacion de `XUI_ADMIN_API_KEY` (fuera del repo).
- Ajuste operativo de permisos del Access Code/API key en XUI si el panel lo requiere.