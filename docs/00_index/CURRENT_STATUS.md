# CURRENT_STATUS

## Estado general (2026-03-11)
- `TASK_054_sincronizacion_indices_post_task_053`: completada (sincronizacion documental validada).
- Tarea activa actual: `TASK_055_xui_account_link_foundation`.
- Implementacion minima backend agregada para resolver identidad XUI por cuenta autenticada:
  - fuente de verdad en cuenta (`xuiLink`) dentro de `AUTH_STORE_FILE`,
  - endpoint protegido `GET /v1/auth/xui/context`,
  - prueba automatizada minima incluida en `backend/test/minimum-foundation.test.js`.
- Bridge de playback (`/v1/content/*`) sin cambios funcionales en esta actualizacion.

## Pendientes de prueba manual
- Validar en entorno real una cuenta autenticada con `xuiLink` cargado en store y confirmar respuesta esperada de `GET /v1/auth/xui/context`.
- Ejecutar smoke manual de playback en TV para confirmar no-regresion funcional posterior a `TASK_055`.

## Riesgo/pendiente externo
- El mapeo inicial `account -> xuiLink` sigue siendo carga manual en store mientras no exista superficie ops dedicada.
- Para validar contra linea real, se requiere conocer el `lineId` existente en XUI; no requiere cambios manuales obligatorios en XUI.
