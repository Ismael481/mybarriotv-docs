# TASK_046_sms_runtime_local_configuration_for_otp_validation

Estado: completed

Fecha:
2026-03-09

## Objetivo
Habilitar configuracion local de credenciales SMS en backend para permitir prueba real de envio OTP desde registro web.

## Alcance
- Configuracion runtime local (`backend/.env`) para proveedor SMS.
- Ajuste puntual en `smsProvider` para interoperabilidad con API zdSMS.
- Sin cambios de arquitectura.

## Archivos tocados
- `backend/.env` (archivo local ignorado por git)
- `backend/src/smsProvider.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_046_sms_runtime_local_configuration_for_otp_validation.md`
- `docs/03_bugs/BUG_016_zdsms_302_redirect_and_local_recipient_format.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- El token/credenciales pueden vencer o ser revocados por proveedor externo.
- En otros entornos, si no se replica configuracion runtime, reaparece el fallo.

## Pendiente de prueba
- Confirmar recepcion de OTP en el telefono destino (validacion fuera de este entorno).

## Resultado esperado
- Desaparece error tecnico previo (`302` HTML) y el backend retorna respuesta operativa SMS.
- `request-otp` responde `ok` con `otpRequestId`.

## Pasos manuales (si existen)
1. Abrir `http://localhost:8080/auth/login`.
2. Completar formulario de registro y pulsar `Enviar SMS`.
3. Validar llegada de OTP.

## Notas
- Las credenciales sensibles quedan solo en `backend/.env` local y no se versionan.
- Validacion tecnica ejecutada:
  - `POST /v1/auth/register/request-otp` con `phone=50632133` -> respuesta exitosa con `otpRequestId`.
