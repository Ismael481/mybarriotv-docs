# TASK_005_bridge_operational_monitoring

Fecha: 2026-03-07
Estado: cerrada (implementada y validada)

## Objetivo
Agregar monitoreo operativo basico del bridge `App TV -> Backend -> XUI` para diagnostico local de disponibilidad antes de abrir login/autenticacion.

## Problema real observado
- En TASK_004 ya se estabilizo el playback, pero faltaba visibilidad operativa resumida para distinguir rapido:
  - fallo upstream temporal
  - timeout
  - respuesta invalida
- Sin este resumen, el diagnostico se mezclaba con pruebas funcionales de app/player.

## Alcance implementado
### Backend
- Se agrego endpoint nuevo:
  - `GET /v1/bridge/ops`
- Se implemento estado operativo en memoria con:
  - estado de configuracion (`config.status`) para detectar errores de env
  - `status` general (`ok`, `degraded`, `unknown`)
  - `lastCheck` (timestamp, route, operation, status)
  - `lastSuccessAt`
  - `lastError` (timestamp, code, message, retryable, upstreamStatus)
  - `counters` (`checks`, `success`, `failures`, `retries`)
- Se enlazo este monitoreo al flujo existente de retries y clasificacion de errores upstream.
- Se mantuvieron estables los contratos de:
  - `GET /v1/content/home`
  - `GET /v1/content/:id/playback`
  - `GET /v1/bridge/health`

### TV app
- Sin cambios funcionales en Home ni playback para esta tarea.

## Fuera de alcance (respetado)
- Login/autenticacion, sesiones, device binding.
- Panel web/dashboard.
- Alertas externas complejas.
- Persistencia historica o base de datos.

## Endpoint creado
- `GET /v1/bridge/ops`

## Ejemplo de respuesta JSON
```json
{
  "requestId": "4f0d7d21-0ca5-4f74-8e57-1f58f8dd0f53",
  "now": "2026-03-07T05:12:02.391Z",
  "config": {
    "status": "ok",
    "error": null
  },
  "status": "degraded",
  "bridge": "xui-first-bridge",
  "bootAt": "2026-03-07T04:58:10.100Z",
  "lastCheck": {
    "at": "2026-03-07T05:11:57.192Z",
    "route": "/v1/content/31/playback",
    "operation": "fetch_playback",
    "status": "error"
  },
  "lastSuccessAt": "2026-03-07T05:09:14.000Z",
  "lastError": {
    "at": "2026-03-07T05:11:57.191Z",
    "code": "TIMEOUT",
    "message": "XUI request timeout after 10000ms",
    "retryable": true,
    "upstreamStatus": null
  },
  "counters": {
    "checks": 18,
    "success": 14,
    "failures": 4,
    "retries": 3
  }
}
```

## Como probar en local
### Caso sano
1. Levantar backend con XUI valido.
2. Llamar:
   - `GET /v1/content/home`
   - `GET /v1/content/31/playback`
   - `GET /v1/bridge/ops`
3. Verificar en `ops`:
   - `status = ok`
   - `lastError = null`
   - contadores incrementando.

### Caso de falla upstream
1. Provocar falla (ejemplo: stream detenido en XUI o credenciales invalidas temporales).
2. Llamar `GET /v1/content/31/playback`.
3. Llamar `GET /v1/bridge/ops`.
4. Verificar en `ops`:
   - `status = degraded`
   - `lastError.code` coherente (`TIMEOUT`, `UPSTREAM_UNREACHABLE`, `INVALID_RESPONSE`, `STREAM_UNAVAILABLE`, etc.)
   - `counters.failures` y `counters.retries` incrementados.

## Validacion ejecutada en sesion
- Caso sano validado:
  - `/v1/content/31/playback` responde URL de stream.
  - `/v1/bridge/ops` devuelve `status=ok` y `lastError=null`.
- Caso falla upstream validado:
  - `/v1/content/31/playback` devolvio `503`.
  - `/v1/bridge/ops` devolvio `status=degraded` con `lastError.code=STREAM_UNAVAILABLE` y `upstreamStatus=404`.

## Limitaciones del monitoreo actual
- Estado solo en memoria del proceso Node.
- Sin persistencia historica al reiniciar backend.
- Sin alertas push ni integracion con observabilidad externa.

## Criterio para pasar a login/auth
- Poder distinguir consistentemente fallos de disponibilidad upstream antes de depurar login.
- Endpoint `ops` estable y util para diagnostico local en pruebas de TV.
- Sin regresiones en Home -> playback.
