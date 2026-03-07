# CHATGPT_CONTEXT

Fecha: 2026-03-07  
Rama: `main`  
Tarea activa: `TASK_011_sms_otp_registration_and_verification`

## Estado corto
- TASK_009 y TASK_010 reflejadas como implementadas.
- TASK_011 agrega registro real OTP SMS en backend + web register conectado.

## Cambios tecnicos clave de TASK_011
- Nuevos endpoints:
  - `/v1/auth/register/request-otp`
  - `/v1/auth/register/verify-otp`
  - `/v1/auth/register/complete`
- Nuevo adaptador SMS desacoplado:
  - `backend/src/smsProvider.js`
  - integra `zdsms` (token + message/send) con credenciales por env
- Nuevo modulo OTP/registro:
  - `backend/src/registrationOtp.js`
- Store auth extendido:
  - cuentas
  - requests OTP
  - sesiones/dispositivos previos
- `register.html` conectado al flujo real.
- `login.html`/signup adaptados visualmente con base `login-form-v34`.
- UX registro reforzado (validacion telefono/password, send OTP condicionado, countdown, bloqueo de campos y cierre de registro en un solo boton).
- Ultimo ajuste: mensajes de error cliente (codigo SMS) sin exponer textos tecnicos tipo `OTP_INVALID`, y correcciones de alineacion/visibilidad en formulario web.
- Ultimo micro-ajuste visual: boton `Enviar SMS` mas ancho + input codigo SMS mas corto para mejor alineacion con campos superiores.
- Ultimo ajuste final visual: fila SMS con grid para alineacion exacta con inputs superiores y boton de ojo sin circulo de fondo.
- Ultimo ajuste puntual: ojo de password en posicion absoluta para visibilidad, `Registrar cuenta` ancho full del formulario y correccion fina de posicion del texto en telefono.
- Ultimo ajuste puntual final: placeholder telefono simplificado y ojo con icono nativo para asegurar visibilidad en todos los entornos.
- Ultimo micro-ajuste: placeholder de password a `Contrasena` y warning en blur cuando no cumple regla de password fuerte.
- Ultimo micro-ajuste: warning en blur para formato invalido de telefono.

## Archivos clave modificados
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js`
- `backend/src/smsProvider.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/register.html`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Variables nuevas principales
- OTP:
  - `AUTH_OTP_*`
  - `AUTH_RL_OTP_*`
- SMS:
  - `SMS_ZDSMS_*`
  - `SMS_TIMEOUT_MS`

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
