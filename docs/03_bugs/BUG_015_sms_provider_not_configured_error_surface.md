# BUG_015_sms_provider_not_configured_error_surface

Estado: closed

Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Cierre
- Bug cerrado tras validacion operativa del flujo OTP SMS.

## Sintoma reportado
- En `/auth/login` al enviar OTP de registro/reset/cambios de cuenta se mostraba:
  - `No se pudo enviar el SMS. Intenta nuevamente.`
- El mensaje era correcto pero insuficiente para diagnostico operativo.

## Causa raiz
- Faltan credenciales de proveedor SMS en backend:
  - `SMS_ZDSMS_API_TOKEN`
  - o `SMS_ZDSMS_EMAIL` + `SMS_ZDSMS_PASSWORD`
- El backend devolvia codigo generico `SMS_SEND_FAILED`, mezclando fallo de configuracion con fallo real de proveedor.

## Correccion aplicada
- Backend:
  - `smsProvider.js` ahora marca error de configuracion con codigo `SMS_PROVIDER_NOT_CONFIGURED`.
  - `registrationOtp.js`, `passwordResetOtp.js`, `accountChangeOtp.js` propagan ese codigo especifico.
- Frontend:
  - `login-render.js` mapea `SMS_PROVIDER_NOT_CONFIGURED` a mensaje claro:
    - `SMS no disponible: falta configurar credenciales del proveedor en backend.`

## Archivos tocados
- `backend/src/smsProvider.js`
- `backend/src/registrationOtp.js`
- `backend/src/passwordResetOtp.js`
- `backend/src/accountChangeOtp.js`
- `apps/web-app/public/auth/assets/login-render.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/03_bugs/BUG_015_sms_provider_not_configured_error_surface.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Validacion
- `npm test` en `backend/`: OK (`5` pruebas, `0` fallos).

## Paso manual externo requerido
- Configurar credenciales SMS reales en entorno backend:
  - opcion A: `SMS_ZDSMS_API_TOKEN`
  - opcion B: `SMS_ZDSMS_EMAIL` y `SMS_ZDSMS_PASSWORD`
- Verificar luego:
  1. abrir `/auth/login`
  2. intentar `Enviar SMS`
  3. confirmar que desaparece `SMS_PROVIDER_NOT_CONFIGURED` y llega OTP real.

## Nota de seguimiento (2026-03-09)
- En `TASK_046` se aplico configuracion local en `backend/.env` para pruebas OTP.
- Se requiere reiniciar proceso backend en ejecucion para que tome estas variables.
