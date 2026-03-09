# BUG_016_zdsms_302_redirect_and_local_recipient_format

Estado: closed

Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Cierre
- Bug cerrado tras validacion de envio OTP real con respuesta exitosa (`otpRequestId`).

## Sintoma reportado
- `POST /v1/auth/register/request-otp` devolvia `SMS_SEND_FAILED` con detalle `SMS provider HTTP 302` y HTML de redireccion.

## Causa raiz
- El request a `POST /v1/message/send` en zdSMS se enviaba sin `Accept: application/json`.
- Para errores de validacion/autenticacion, el proveedor devolvia redireccion HTML (`302`) en lugar de JSON.
- Numeros locales de 8 digitos se enviaban sin prefijo de pais (`53`), provocando validacion de formato incorrecto.

## Correccion aplicada
- `backend/src/smsProvider.js`:
  - agrega header `Accept: application/json` en token y send.
  - normaliza destinatario `XXXXXXXX -> 53XXXXXXXX`.
  - prioriza token fresco por `SMS_ZDSMS_EMAIL/SMS_ZDSMS_PASSWORD` cuando estan disponibles.

## Archivos tocados
- `backend/src/smsProvider.js`
- `docs/03_bugs/BUG_016_zdsms_302_redirect_and_local_recipient_format.md`
- `docs/02_tasks/TASK_046_sms_runtime_local_configuration_for_otp_validation.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion
- Prueba manual endpoint:
  - `POST /v1/auth/register/request-otp` con `phone=50632133` retorna `otpRequestId` y expiracion.

## Paso manual externo requerido
- Confirmar recepcion del SMS en el dispositivo del usuario final.
