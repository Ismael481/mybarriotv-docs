# BUG_006_register_otp_pending_state_lost_on_reload

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (duplicacion de solicitudes OTP y confusion de estado de registro)

Descripcion:
En registro web, al enviar OTP y recargar pagina, el estado podia quedar inconsistente: countdown congelado/percibido como pausado y posibilidad de reintentar flujo sin respetar solicitud pendiente de backend.

Causa raiz:
- El frontend dependia de estado local parcial para countdown/bloqueo.
- El backend permitia crear nuevas solicitudes OTP sin validar solicitud de registro abierta para el mismo contexto (`phone/username`).

Correccion aplicada:
- Backend:
  - se agrega deteccion de solicitud OTP abierta (`pending|verified`) y rechazo con `OTP_REQUEST_PENDING` + metadata de expiracion/ID.
  - `POST /v1/auth/register/request-otp` ahora retorna metadata de solicitud pendiente en errores.
- Frontend:
  - recupera solicitud pendiente y reanuda countdown tras recarga.
  - bloquea `username/phone/password` durante cooldown.
  - limpia estado OTP local al expirar/completar.

Archivos:
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js`
- `backend/src/server.js`
- `apps/web-app/public/auth/login.html`

Validacion pendiente:
- Prueba manual integral en navegador con recarga intermedia y reintentos de OTP.
