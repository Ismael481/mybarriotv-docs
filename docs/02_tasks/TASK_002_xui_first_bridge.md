# TASK_002_xui_first_bridge

Fecha: 2026-03-06
Estado: implementacion inicial completada (pendiente validacion E2E en entorno XUI)

## Objetivo
Implementar el primer bridge funcional minimo `App TV -> Backend -> XUI` para Home catalog minimo, seleccion de item y playback de prueba, evitando integracion directa de la TV app con XUI.

## Alcance
- Backend minimo con endpoints:
  - `GET /v1/content/home`
  - `GET /v1/content/:id/playback`
- Cliente XUI en backend con mapper a contrato estable.
- Logs claros de inicio, exito y error en llamadas bridge.
- Manejo basico de errores upstream XUI.
- TV app con feature flag `BridgeEnabled` para alternar demo/backend.
- Integracion de Home y playback via backend cuando `BridgeEnabled=true`.

## Fuera de alcance
- Autenticacion real, sesiones y renovacion de tokens.
- Vinculacion de dispositivo y control comercial.
- Panel web y portal cliente.
- Integracion de todas las pantallas.
- Refactor grande de arquitectura o cambios visuales no necesarios.
- Logica de negocio amplia.

## Restricciones
- Sin llamadas directas App TV -> XUI.
- Primer flujo pequeno, read-only, bajo riesgo y reemplazable.
- Contrato backend estable, sin exponer payload crudo XUI.

## Dependencias
- Node.js 18+ para ejecutar backend minimo.
- Entorno XUI con endpoint de Home y playback.
- Variables de entorno backend (`XUI_BASE_URL` obligatoria).
- TV app apuntando a backend (`BACKEND_BASE_URL`, por defecto `http://10.0.2.2:8080`).

## Riesgos
- Diferencias de payload real XUI respecto al mapper generico actual.
- `id` no numerico de XUI: se mapea internamente a int para conservar navegacion actual.
- Si XUI no responde stream valido, playback falla en modo bridge.
- Validacion Android local bloqueada en este entorno por JBR faltante.

## Criterio de exito
- Home carga desde backend cuando `BridgeEnabled=true`.
- Seleccion de item resuelve playback via backend.
- Stream inicia reproduccion sin llamada directa App TV -> XUI.
- Logs backend muestran consulta real a XUI y resultado.

## Prueba minima
1. Configurar backend con `XUI_BASE_URL` y correr `npm start` en `backend/`.
2. En TV app, activar `BridgeEnabled=true` en `app/build.gradle.kts`.
3. Ejecutar app y abrir Home.
4. Seleccionar item y reproducir stream.
5. Confirmar en logs backend:
   - `Bridge call started` para `/v1/content/home` y `/v1/content/{id}/playback`
   - `Bridge call success`
6. Validar reproduccion >= 10 segundos.

## Propuesta del primer flujo integrado
1. TV app (`BridgeEnabled=true`) solicita `GET /v1/content/home` a backend.
2. Backend consulta XUI, mapea respuesta y devuelve `sections/items` estables.
3. TV app muestra lista minima.
4. Usuario selecciona item.
5. TV app solicita `GET /v1/content/{id}/playback` al backend.
6. Backend consulta XUI, mapea playback y devuelve URL de stream.
7. Player reproduce stream.

## Impacto esperado
### App TV
- Integracion minima backend para Home + playback con fallback a demo.
- Feature flag `BridgeEnabled` para activar/desactivar bridge sin refactor.

### Backend
- Bridge minimo operativo a XUI con mapper y logs.

### XUI
- Sigue como motor de contenido; no se expone internamente al cliente TV.

## Orden recomendado de implementacion futura
1. Ajustar mapper con payload real de XUI del entorno final.
2. Agregar tests de contrato backend para home/playback.
3. Extender flujo a detalle de contenido manteniendo contrato estable.
4. Luego avanzar a auth/sesiones/device binding en tareas separadas.

## Archivos tocados en esta implementacion
- `backend/package.json`
- `backend/.env.example`
- `backend/README.md`
- `backend/src/server.js`
- `backend/src/xuiClient.js`
- `backend/src/mappers.js`
- `apps/tv-app/app/build.gradle.kts`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeDtos.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/BridgeCatalogRepository.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/details/ProductViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`

## Pendiente de prueba
- Prueba E2E real con endpoint XUI definitivo.
- Confirmar arranque de stream en dispositivo objetivo por >= 10s.

## Resultado esperado
Bridge minimo funcional listo para validar en entorno integrado, con fallback demo controlado por feature flag.

## Pasos manuales externos (XUI/configuracion)
1. Confirmar endpoint real XUI para Home y playback.
2. Configurar `XUI_BASE_URL`, `XUI_HOME_PATH` y `XUI_PLAYBACK_PATH_TEMPLATE` en backend.
3. Proveer credencial (`XUI_API_KEY`) si aplica.
4. Verificar que XUI entrega al menos un item reproducible en entorno de prueba.
