# CURRENT_STATUS

## Estado general (2026-03-09)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Backend auth/ops modularizado incrementalmente: operativo.
- Web auth/admin modularizado y usable hasta `TASK_045`: operativo.
- Suite minima automatizada (`npm test` backend): estable en verde.

## Estado OTP SMS (TASK_046)
- Causas confirmadas del fallo SMS:
  - backend activo sin credenciales runtime actualizadas.
  - `smsProvider` no enviaba `Accept: application/json` al proveedor (respuesta `302` HTML).
  - telefono local de 8 digitos se enviaba sin prefijo `53`.
- Correccion aplicada:
  - `backend/.env` local actualizado con credenciales SMS de prueba.
  - `backend/src/smsProvider.js` ajustado para:
    - priorizar token fresco por `email/password` cuando estan disponibles,
    - enviar header `Accept: application/json`,
    - normalizar `recipient` local `XXXXXXXX -> 53XXXXXXXX`.
- Resultado:
  - `POST /v1/auth/register/request-otp` para `50632133` responde OK con `otpRequestId` (mensaje encolado).

## Estado UX OTP web (TASK_047)
- Problema reportado en reload de `/auth/login` (registro/reset):
  - contador OTP quedaba inconsistente visualmente.
  - inputs de identidad quedaban bloqueados y vacios, impidiendo contexto de verificacion.
- Correccion aplicada en frontend web:
  - estado OTP ahora persiste tambien `flow` e identidad basica (`username/phone`) por flujo.
  - al recargar con OTP activo, el formulario restaura esos datos y mantiene bloqueo correcto de identidad.
  - al expirar OTP, se limpia estado persistido y se desbloquea formulario para nuevo request.
  - limpieza de campos sensibles al cargar ya no borra inputs del formulario OTP pendiente.
- Alcance:
  - sin cambios de contrato backend.
  - sin cambios de reglas de negocio OTP (TTL sigue 5 minutos).

## Estado demo trial en TV (TASK_048)
- Regla aplicada:
  - el demo de cuenta `trial` inicia solo en el primer login desde TV.
  - login/registro web no inicia el contador demo.
- Correcciones backend:
  - `upsertAccount` ya no fija `trialStartedAt/expiresAt` al crear cuenta.
  - nuevo inicio explicito de trial en primer login TV (`manual_login` y `qr_exchange`).
  - normalizacion de cuenta deja de usar `createdAt` como `trialStartedAt` implicito.
- Validacion:
  - prueba tecnica con store temporal confirma:
    - cuenta nueva `trial` -> `expiresAt=null`,
    - tras `startAccountTrialOnFirstTvLogin` -> se setean `trialStartedAt` + `expiresAt`.

## Estado admin/web trial expiry (TASK_049)
- Problema detectado:
  - en panel admin, al aplicar estado `trial` sin enviar `expiresAt`, backend generaba expiracion por defecto.
  - esto violaba regla de negocio: demo no inicia hasta primer login en TV.
- Correccion aplicada:
  - se elimino fallback `ops_trial_expiry_default` en `handleOpsAccountStatusUpdate`.
  - normalizacion de cuenta fuerza `expiresAt=null` cuando:
    - `accountStatus=trial`
    - sin `trialStartedAt`
    - sin `demoConsumedAt`
  - `updateAccountStatus` conserva esa regla para transiciones a `trial` sin inicio demo.
- Resultado:
  - web/admin ya no activan ni adelantan expiracion de demo en cuentas trial sin login TV.

## Estado QR TV base URL (TASK_050)
- Problema detectado:
  - `qrUrl` salia con `http://localhost:8080/...`, no accesible desde movil al escanear QR.
- Causa:
  - `AUTH_WEB_BASE_URL` local estaba apuntando a `localhost`.
- Correccion aplicada:
  - `backend/.env` actualizado a `AUTH_WEB_BASE_URL=http://10.10.6.121:8080`.
  - backend reiniciado para tomar configuracion runtime.
- Validacion:
  - `POST /v1/auth/device/start` devuelve `qrUrl` con `http://10.10.6.121:8080/auth/device-approve?...`.

## Estado integracion XUI runtime (TASK_051)
- Configuracion aplicada en `backend/.env`:
  - `XUI_BASE_URL=http://panel.mybarriotv.com`
  - `XUI_MODE=xtream`
  - `XUI_USER=bridge_test`
  - `XUI_PASS` operativo para la linea bridge
  - `XUI_API_KEY` cargado (opcional en modo xtream)
- Correccion tecnica aplicada:
  - loader `.env` en backend ahora usa valor de archivo cuando la variable de entorno existe pero esta vacia.
- Validacion:
  - `GET /v1/content/home` devuelve secciones/items reales de XUI (catalogo cargado).

## Estado playback XUI (TASK_052)
- Problema detectado:
  - backend devolvia playback `.../live/<user>/<pass>/<id>.m3u8`.
  - en este panel XUI los streams validos salen firmados como `.../play/<token>/m3u8`.
- Causa raiz:
  - resolucion xtream de playback construia URL generica `/live/...` en lugar de usar playlist firmada.
  - ademas `XUI_API_KEY` en modo xtream interferia en lectura de playlist.
- Correccion aplicada:
  - `xuiClient` ahora intenta resolver playback por nombre desde playlist `.../playlist/<user>/<pass>/m3u?output=hls`.
  - si encuentra match, devuelve URL firmada `/play/<token>/m3u8`.
  - `XUI_API_KEY` local se deja vacio en modo `xtream`.
  - runtime local ajustado a `XUI_OUTPUT=ts` para compatibilidad con reproduccion TV en entorno actual.
- Resultado:
  - `GET /v1/content/31/playback` ahora devuelve URL `http://panel.mybarriotv.com:80/play/.../ts` (`streamType=mpegts`).

## Validacion tecnica
- Validacion funcional manual OTP: OK (request-otp exitoso con `otpRequestId`).
- Sintaxis JS validada en assets tocados con `node --check`.
- `npm test` backend: OK (`5` pruebas, `0` fallos).
- `npm test` backend actualizado: OK (`6` pruebas, `0` fallos).
- Nuevo test de regresion:
  - `ops trial status update does not auto-assign expiresAt before first TV login`.
- Validacion runtime QR: OK con `qrUrl` LAN.
- Validacion runtime XUI: OK (`/v1/content/home` con payload real).
- Validacion runtime playback: OK (`/v1/content/31/playback` devuelve URL firmada).
- `npm test` backend: OK (`6` pruebas, `0` fallos).

## Riesgo/pendiente externo
- La validez final de envio depende del proveedor externo (zdSMS) y del token/credenciales vigentes.
- Validar manualmente en TV fisica: primer login inicia demo y accesos web posteriores no reinician ni adelantan conteo.
