# TASK_002_xui_first_bridge

Fecha: 2026-03-06
Estado: completada y validada en TV fisica

## Objetivo
Implementar y validar el primer bridge funcional minimo `App TV -> Backend -> XUI` para Home catalog minimo, seleccion de item y playback de prueba.

## Alcance ejecutado
- Backend minimo con endpoints:
  - `GET /v1/content/home`
  - `GET /v1/content/:id/playback`
- Mapeo de respuesta XUI a contrato estable para TV app.
- Integracion TV app con feature flag `BridgeEnabled`.
- Fallback a demo cuando bridge esta desactivado.
- Validacion E2E en TV fisica con stream real.

## Fuera de alcance (mantenido)
- Autenticacion/sesiones/device binding.
- Control comercial, panel web y portal cliente.
- Refactor de arquitectura amplia.

## Restricciones cumplidas
- Sin llamada directa `App TV -> XUI`.
- Flujo pequeno, read-only y reemplazable.

## Dependencias
- Backend LAN activo (`10.10.6.121:8080`) para pruebas locales.
- XUI con linea de prueba `bridge_test` y stream `test1` (`stream_id=31`).

## Riesgos detectados
- El stream en XUI puede pausarse/caerse y requerir restart manual.
- Si backend no esta disponible en LAN, la app vuelve a fallback demo.

## Criterio de exito (resultado)
- App ya no depende solo de mock en el flujo minimo: **cumplido**.
- App consume backend propio para home/playback: **cumplido**.
- Backend consulta XUI y oculta detalles internos: **cumplido**.
- Reproduccion en TV >= 10s: **cumplido**.

## Prueba minima ejecutada
1. Backend levantado con modo Xtream y credenciales de linea de prueba.
2. `GET /v1/content/home` devuelve `test1`.
3. `GET /v1/content/31/playback` devuelve URL de stream valida.
4. En TV fisica se visualiza `test1` y se reproduce correctamente.

## Impacto por componente
### App TV
- Bridge activo por flag y reproduccion validada.
- Se habilito `usesCleartextTraffic=true` para entorno LAN HTTP.

### Backend
- Bridge operativo para home/playback con logs claros.
- Soporte para payload Xtream en fase minima.

### XUI
- Funciona como motor de contenido.
- Requiere monitoreo operativo del stream de prueba.

## Orden recomendado de implementacion futura
1. Mejorar estabilidad (health/retry cuando stream cae en XUI).
2. Extender flujo a detalle/categorias conservando contrato backend.
3. Luego abordar auth/sesiones/device binding en tareas separadas.

## Archivos tocados
- `backend/package.json`
- `backend/.env.example`
- `backend/README.md`
- `backend/src/server.js`
- `backend/src/xuiClient.js`
- `backend/src/mappers.js`
- `apps/tv-app/app/build.gradle.kts`
- `apps/tv-app/app/src/main/AndroidManifest.xml`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeDtos.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/data/BackendBridgeApi.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/bridge/BridgeCatalogRepository.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/home/HomeViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/details/ProductViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerViewModel.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/player/PlayerScreen.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`

## Pendiente de prueba
- Ninguna critica para esta fase minima.

## Resultado esperado
Primer bridge `App TV -> Backend -> XUI` validado y listo para siguiente iteracion de estabilizacion.

## Pasos manuales externos (XUI/configuracion)
1. Reiniciar stream de prueba en XUI cuando quede pausado/caido.
2. Mantener linea de prueba habilitada durante demos tecnicas.
3. Rotar password de linea de prueba tras finalizar pruebas.
