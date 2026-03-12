# TASK_060_xui_ops_provisioning_hardening

Estado: in_progress

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
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_060_xui_ops_provisioning_hardening.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Implementacion realizada en esta iteracion
- Apertura formal de `TASK_060` como siguiente tarea activa unica.
- Definicion de alcance, restricciones, riesgos y prueba minima para hardening del flujo existente.
- Sin cambios de logica en backend, TV app ni web app en esta apertura.

## Validacion ejecutada (runtime local)
- Backend activo en `http://127.0.0.1:8080` (`/health` = `200`).
- Login operador (`demo`) y acceso a `GET /v1/auth/ops/accounts`: OK.
- Caso exitoso provisioning (`POST /v1/auth/ops/accounts/:accountId/xui/provision`):
  - `ok=true`, `createdInXui=true`.
  - linea creada en XUI con `lineId` real (evidencia local: ids `7`, `8`, `9` durante pruebas).
- Caso de error controlado (username duplicado con `forceProvision=true`):
  - respuesta `502` con `code=XUI_ADMIN_ACTION_FAILED`.
  - mapeo de error operacional backend confirmado.
- Pruebas automatizadas backend:
  - `npm test`: OK (`9` tests, `0` fallos).

## Riesgos
- Diferencias entre paneles XUI para campos de bouquet/fecha pueden romper create_line si no se mapean correctamente.
- Mapeo de errores insuficiente puede ocultar causa operativa real (permiso, parametro, timeout o redirect).
- Mantener API key expuesta en pruebas representa riesgo operativo hasta que se rote.

## Hallazgos de hardening (que falta)
- `bouquetIds` invalido (`999999`) no fue rechazado y el panel respondio `STATUS_SUCCESS`.
- Falta validacion minima previa contra bouquets reales (o estrategia equivalente de verificacion) para evitar aprovisionar lineas con paquetes no esperados.
- Falta validar explicitamente caso de permisos insuficientes del Access Code/API key en runtime de tarea (no ejecutado en esta corrida).

## Pendiente de prueba minima
- Validacion ya ejecutada:
  - 1 caso exitoso de provisioning.
  - 1 caso de error controlado con mapeo `XUI_ADMIN_ACTION_FAILED`.
- Pendiente para cierre:
  - definir e implementar (si aplica) validacion minima de bouquets contra catalogo real.
  - ejecutar evidencia de permiso insuficiente (Access Code/API key) o documentar limitacion operativa si no se puede reproducir sin tocar entorno.

## Resultado esperado
- Base mas robusta para operar provisioning existente sin romper el bridge principal ya validado.
- Documentacion clara de limites, riesgos y manejo minimo de diferencias entre paneles XUI.
- Cualquier ajuste de implementacion adicional debe ser minimo, acotado y reversible.

## Pasos manuales si existen
1. Rotar `XUI_ADMIN_API_KEY` en XUI y en la configuracion del backend.
2. Verificar Access Code y permisos API para acciones de provisioning.
3. Confirmar en panel objetivo nombres de parametros para bouquets/paquetes antes de cambiar mapeos.

## Cambios externos XUI/config requeridos
- La rotacion de `XUI_ADMIN_API_KEY` ocurre fuera del repo y debe quedar aplicada en el entorno backend.
- Si la variante de panel usa parametros no estandar de bouquets, documentar mapping acordado antes de tocar codigo.
