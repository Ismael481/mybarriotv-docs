# TASK_058_xui_admin_api_discovery_for_auto_line_provisioning

Estado: completed

Fecha de creacion:
2026-03-11

Ultima actualizacion:
2026-03-11

## Objetivo
Confirmar y documentar la superficie real de Admin API XUI necesaria para implementar provision automatica de lineas desde backend (Fase 1), manteniendo el alcance acotado y sin romper el flujo actual.

## Alcance
- Analizar insumo API entregado por el usuario:
  - `C:\Users\dcemm\Downloads\API de XUI.ONE y Xtream UI.pdf`
- Extraer endpoints/acciones utiles para provisioning:
  - `create_line`
  - `get_line` / `get_lines`
  - `get_bouquets`
  - `edit_line`, `disable_line`, `enable_line`, `delete_line` (operaciones de ciclo de vida)
- Definir lista minima de datos externos requeridos para implementacion backend.
- Actualizar indices y contexto documental.

## Fuera de alcance en esta tarea
- Implementar codigo backend de auto-provision.
- Cambiar registro/login actual.
- Cambiar arquitectura global.
- Cerrar `TASK_057` (sigue pendiente validacion manual de playback en TV).

## Hallazgos tecnicos (discovery)
- Patron base Admin API (segun PDF):
  - `{proto}://HOST:{port}/{access_code}/?api_key=API_KEY&action=ACCION`
- Metodo esperado para alta:
  - `POST` con `action=create_line` y body tipo form data.
- Campos minimos observados para create:
  - `username`
  - `password`
- Campos opcionales observados:
  - `exp_date`
  - `max_connections`
  - `bouquets_selected[]`
  - `isp_lock` (u otros segun variante)
- Respuesta esperada de alta (ejemplo documental):
  - `status=STATUS_SUCCESS`
  - `data.line_id` (o campo equivalente)
- Hallazgo de seguridad:
  - `api_key` en query string; requiere redaction en logs.
  - posible restriccion por IP allowlist en Access Code.
  - HMAC puede existir segun variante del panel (no confirmado aun en entorno real).

## Evidencia runtime (entorno del usuario)
- Prueba real ejecutada con:
  - base: `http://panel.mybarriotv.com/NtAFVMmW/`
  - `action=user_info`, `action=get_bouquets`, `action=create_line`
- Resultado observado:
  - respuesta HTML de login de panel en lugar de JSON API.
  - verificacion tecnica adicional: `302 Location: ./login` para la ruta base de Access Code.
- Inferencia operativa:
  - la ruta/credencial usada no esta siendo aceptada como Admin API activa para ese host/path.
  - antes de implementar backend de provision se debe corregir y validar Access URL/API key reales del panel.

## Archivos tocados
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_058_xui_admin_api_discovery_for_auto_line_provisioning.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- La guia del PDF es base comunitaria; puede diferir de la variante exacta del panel operativo.
- Si se implementa sin validar respuesta real de `create_line`, puede romper idempotencia o enlazar mal `xuiLineId`.
- Dependencia de configuracion externa (XUI) fuera del repo.

## Pendiente de prueba
- Probar en panel real:
  1. `action=user_info`
  2. `action=get_bouquets`
  3. `action=create_line` (linea de prueba)
- Confirmar:
  - formato real de `exp_date`
  - clave exacta donde regresa el id de linea
  - reglas reales de unicidad (username/otros)
  - si requiere HMAC y formato exacto
  - confirmar que la respuesta sea JSON (`STATUS_SUCCESS`) y no redireccion a `./login`

## Resultado esperado
- Documento de discovery cerrado con especificacion minima validada para empezar implementacion backend de Fase 1 (`create+link`) sin adivinar parametros.

## Cierre
- Discovery validado en panel real:
  - `user_info` OK con `Access Code` operativo `lHpqPGtQ`.
  - `get_bouquets` OK.
  - `create_line` OK (`STATUS_SUCCESS`).
- Se habilita implementacion en `TASK_059` para endpoint backend de provisioning+link.

## Pasos manuales si existen
1. En panel XUI, abrir `Management -> Access Control` y copiar la URL exacta del Access Code de tipo API.
2. Verificar que el Access Code este habilitado y con IP allowlist compatible con la IP del backend/operador.
3. Desde panel XUI (o Postman/cURL), ejecutar `user_info` para validar Access Code y API key.
4. Ejecutar `get_bouquets` y registrar ids reales de paquete a usar en alta.
5. Ejecutar `create_line` de prueba y guardar request/response reales (redactando secretos).
6. Compartir evidencias para iniciar implementacion backend.

## Cambios externos XUI/config requeridos
- Confirmar o crear Access Code con permisos Admin API.
- Confirmar API key activa para acciones de lineas (`create_line`, `get_line`, `get_bouquets`).
- Si aplica, permitir IP del backend en allowlist del Access Code.
- Si aplica HMAC, documentar:
  - headers obligatorios (`X-API-KEY`, `X-Signature`, `X-Timestamp`)
  - string canonico para firmar
  - algoritmo
