# TASK_002_xui_first_bridge

Fecha: 2026-03-06
Estado: definido (documentacion), pendiente implementacion

## Objetivo
Definir el primer bridge minimo entre la TV app y XUI manteniendo desde el inicio el patron `App TV -> Backend propio -> XUI`, para empezar a reemplazar mock/demo por contenido real sin integrar todo el producto.

## Alcance
- Disenar el primer flujo tecnico minimo, read-only y verificable.
- Definir contrato inicial entre TV app y backend para ese flujo.
- Definir rol minimo del backend frente a XUI.
- Dejar criterios de prueba minima antes de ampliar integracion.
- Documentar dependencias y pasos manuales externos de preparacion en XUI.

## Fuera de alcance
- Autenticacion real, sesiones y renovacion de tokens.
- Vinculacion de dispositivo y control comercial.
- Panel web y portal cliente.
- Integracion total de pantallas/menus de la TV app.
- Refactor grande de arquitectura o cambios visuales.
- Logica de negocio amplia en backend.

## Restricciones
- Prohibido acceso directo `TV app -> XUI`.
- El primer bridge debe ser pequeno, read-only, de bajo riesgo y reemplazable.
- Mantener cambios futuros acotados a un flujo vertical minimo.
- No acoplar la app TV a estructuras internas de XUI.

## Propuesta del primer flujo a integrar
Flujo recomendado: **Home catalog minimo (lista simple) + seleccion de item + apertura de stream de prueba**, siempre via backend.

Secuencia:
1. TV app solicita `GET /v1/content/home` al backend.
2. Backend obtiene contenido desde XUI (o adaptador XUI) y responde un contrato propio estable.
3. TV app muestra lista minima (id, titulo, poster, tipo).
4. Usuario selecciona item y TV app solicita `GET /v1/content/{id}/playback`.
5. Backend resuelve/normaliza URL de stream de prueba desde XUI y devuelve solo datos necesarios para reproducir.
6. TV app abre reproductor sin conocer endpoints ni tokens internos de XUI.

Contrato minimo sugerido (fase 1):
- `GET /v1/content/home`
  - response: `sections[] -> items[] { id, title, image, contentType }`
- `GET /v1/content/{id}/playback`
  - response: `{ contentId, playbackUrl, streamType, drm: false }`

## Dependencias
- Backend propio con endpoint minimo expuesto para TV app.
- Adaptador o cliente XUI en backend (no en TV app).
- Entorno XUI con catalogo de prueba y al menos 1 stream reproducible.
- Definicion de mapeo XUI -> contrato interno backend.

## Riesgos
- Riesgo de acoplar backend al payload crudo de XUI si no se define mapper estable.
- Riesgo de inestabilidad de stream de prueba en entorno XUI.
- Riesgo de que la TV app mezcle flujo nuevo con `DemoCatalogRepository` sin feature flag claro.
- Riesgo de crecimiento de alcance (scope creep) si se agregan auth/sesiones en esta fase.

## Criterio de exito
- La TV app deja de depender solo de mock para al menos 1 flujo real.
- La TV app consume exclusivamente backend propio para ese flujo.
- Backend consulta/prepara integracion con XUI y devuelve contrato interno.
- La TV app no conoce detalles internos de XUI.
- El flujo puede probarse end-to-end con un stream de prueba reproducible.

## Prueba minima
Caso E2E minimo:
1. Abrir Home en TV app con feature flag de bridge activo.
2. Ver al menos 1 seccion con items provenientes del backend.
3. Seleccionar un item y abrir playback usando `GET /v1/content/{id}/playback`.
4. Confirmar reproduccion minima (inicio de stream >= 10s sin crash).
5. Confirmar en logs que no hubo llamada directa de la TV app a XUI.

## Impacto esperado por componente
### App TV
- Agregar cliente a backend para home minimo y playback minimo.
- Mantener UI actual, cambiando solo proveedor de datos del flujo elegido.
- Mantener fallback controlado a mock (feature flag) durante transicion.

### Backend
- Exponer endpoints `v1/content/home` y `v1/content/{id}/playback`.
- Implementar adapter XUI y mapeo a contrato interno.
- Centralizar errores y normalizacion basica de respuesta.

### XUI
- Proveer fuente de catalogo de prueba y stream valido para entorno de integracion.
- No exponer detalles operativos de XUI al cliente TV.

## Orden recomendado de implementacion futura
1. Backend: definir contrato interno y mocks de respuesta estables.
2. Backend: conectar adapter XUI para `home` read-only.
3. App TV: consumir `GET /v1/content/home` con flag.
4. Backend: habilitar `GET /v1/content/{id}/playback` con stream de prueba.
5. App TV: abrir player con response de backend.
6. Ejecutar prueba minima E2E y congelar baseline.

## Archivos tocados
- `docs/02_tasks/TASK_002_xui_first_bridge.md`
- `docs/04_decisions/ADR_003_first_bridge_read_only_home_catalog.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Pendiente de prueba
- Validar en implementacion que el flujo corre sin dependencia directa a XUI desde la app TV.
- Validar estabilidad del stream de prueba definido para el entorno tecnico.

## Resultado esperado
Plan tecnico minimo documentado y listo para implementacion incremental del primer bridge real `TV app -> Backend -> XUI`.

## Pasos manuales externos (XUI/configuracion)
1. Crear o identificar en XUI una categoria de prueba para Home minimo.
2. Asegurar al menos 1 item con metadata basica: id, titulo, imagen, tipo.
3. Asegurar al menos 1 stream de prueba reproducible para ese item.
4. Entregar al equipo backend los datos de entorno necesarios (base URL, credenciales de servicio, identificadores de catalogo).
5. Validar desde backend que XUI responde correctamente antes de conectar la TV app.
