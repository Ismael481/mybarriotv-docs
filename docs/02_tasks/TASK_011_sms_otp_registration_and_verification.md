# TASK_011_sms_otp_registration_and_verification

Fecha: 2026-03-07  
Estado: implementada (pendiente validacion final en entorno del usuario)

## Objetivo
Implementar registro real con verificacion OTP por SMS manteniendo backend como centro de auth y sin romper login manual/QR.

## Alcance implementado

### Backend
- Endpoints nuevos:
  - `POST /v1/auth/register/request-otp`
  - `POST /v1/auth/register/verify-otp`
  - `POST /v1/auth/register/complete`
- Expiracion OTP, max intentos, limpieza de requests expandidas y rate limiting basico.
- Auditoria minima en store (`auditEvents`) para request/verify/complete/fallos de envio.
- Auth login ahora soporta cuentas registradas (usuario o telefono + password) sin perder usuario demo existente.

### Integracion SMS
- Nuevo adaptador desacoplado: `backend/src/smsProvider.js`.
- Integracion real `zdsms` via:
  - `POST /v1/token`
  - `POST /v1/message/send`
- Credenciales solo por env, sin hardcode.
- Manejo de error controlado (`SMS_SEND_FAILED`).
- Ajuste aplicado: `recipient` se normaliza a digitos (`535...`) para compatibilidad con formato esperado del proveedor.
- Ajuste aplicado: backend carga `.env` automaticamente al arrancar para evitar errores por variables faltantes en `npm start`.

### Persistencia
- Store JSON ampliado en `authPersistence` para:
  - cuentas (`accountsById`, indices username/phone)
  - requests OTP (`otpRequestsById`)
  - sesiones/dispositivos previos

### Web app
- `register.html` conectado a flujo real:
  - telefono
  - usuario
  - password
  - solicitar OTP
  - verificar OTP
  - completar registro (activa cuenta + devuelve JWT)
- UI mantiene estilo y simplicidad.
- Ajuste visual aplicado: `/auth/login` y signup adaptados con base `login-form-v34` de `awesome-login-pages`.
- Ajustes UX/validacion:
  - login y registro en espanol
  - boton `Registrarse` junto al boton `Entrar` en login
  - flujo de registro simplificado a 2 acciones: `Enviar SMS` + `Registrar cuenta`
  - boton OTP alineado junto al input OTP
  - boton OTP deshabilitado hasta completar inputs requeridos y validos
  - validacion telefono: admite `+53XXXXXXXX` o `XXXXXXXX` (8 digitos locales)
  - validacion password fuerte: minimo 8 con mayuscula, minuscula, numero y simbolo
  - mostrar/ocultar password en registro
  - bloqueo de campos de identidad tras envio OTP
  - contador regresivo de expiracion OTP (5 min) y reenvio al expirar
  - al completar registro exitoso, boton cambia a `Ir a iniciar sesion`
  - errores tecnicos (`OTP_INVALID`, etc.) mapeados a mensajes amigables para cliente (codigo SMS invalido, vencido, etc.)
  - fallback adicional de mapeo cuando backend devuelve texto libre (ej. `Invalid OTP code`) para mantener mensaje cliente consistente
  - ajuste fino de alineacion (input codigo SMS + boton) y mejora de contraste del icono mostrar/ocultar contrasena
  - micro-ajuste final de layout solicitado por usuario: input de codigo SMS un poco mas corto y boton `Enviar SMS` un poco mas ancho para alineacion visual con los inputs superiores
  - ajuste final adicional: fila SMS convertida a grilla fija de 380px para alinear borde izquierdo/derecho con los inputs superiores; icono de password queda en estilo ojo simple (sin circulo de fondo)
  - ajuste visual puntual final: icono ojo reposicionado en overlay para visibilidad, boton `Registrar cuenta` a ancho completo del input y texto del telefono desplazado ligeramente a la izquierda para mejor alineacion visual
  - ajuste puntual final de copy/visibilidad: placeholder de telefono simplificado a `Telefono +53XXXXXXXX` y boton ojo cambiado a icono nativo visible (sin dependencia de fuente externa)
  - micro-ajuste final de registro: placeholder de password simplificado a `Contrasena` y aviso al salir del campo cuando no cumple politica de password fuerte
  - micro-ajuste UX adicional: aviso en blur del campo telefono cuando no cumple formato requerido (`+53XXXXXXXX` o `XXXXXXXX`)
  - mensaje SMS actualizado a "codigo de verificacion" (sin termino OTP visible al cliente)

### TV app
- Sin cambios de arquitectura ni rediseño.
- Login manual y QR se mantienen funcionales.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js` (nuevo)
- `backend/src/smsProvider.js` (nuevo)
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/register.html`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Variables de entorno requeridas
- OTP/Auth:
  - `AUTH_OTP_TTL_SECONDS`
  - `AUTH_OTP_MAX_ATTEMPTS`
  - `AUTH_OTP_RETENTION_SECONDS`
  - `AUTH_RL_OTP_REQUEST_MAX`, `AUTH_RL_OTP_REQUEST_WINDOW_MS`
  - `AUTH_RL_OTP_VERIFY_MAX`, `AUTH_RL_OTP_VERIFY_WINDOW_MS`
  - `AUTH_RL_OTP_COMPLETE_MAX`, `AUTH_RL_OTP_COMPLETE_WINDOW_MS`
  - `AUTH_OTP_DEBUG_CODE` (solo pruebas locales)
- SMS:
  - `SMS_ZDSMS_BASE_URL`
  - `SMS_ZDSMS_API_TOKEN`
  - `SMS_ZDSMS_EMAIL`
  - `SMS_ZDSMS_PASSWORD`
  - `SMS_TIMEOUT_MS`

## Flujo completo
1. Web llama `request-otp` con telefono/usuario/password.
2. Backend crea OTP request persistida y envia SMS via provider.
3. Web llama `verify-otp` con codigo.
4. Backend valida codigo, intentos y expiracion.
5. Web llama `complete`.
6. Backend crea/activa cuenta persistida y devuelve JWT.
7. Usuario puede hacer login manual web/TV o aprobar login QR.

## Como probar en local
1. En backend `.env`:
   - `SMS_ZDSMS_BASE_URL=https://zdsms.cu/api`
   - `SMS_ZDSMS_EMAIL=<tu_email_zdsms>`
   - `SMS_ZDSMS_PASSWORD=<tu_password_zdsms>`
   - opcional dev: `AUTH_OTP_DEBUG_CODE=123456`
2. Abrir `/auth/register`.
3. Llenar telefono/usuario/password y solicitar OTP.
4. Verificar con `123456`.
5. Completar registro.
6. Probar login en `/auth/login` o TV con esas credenciales.

## Pendiente antes de perfiles
- Ajustar politicas productivas de cuenta (bloqueo, recuperacion, etc.).
- Migrar persistencia de cuentas/otp a DB productiva.
- Endurecer controles anti-abuso y observabilidad avanzada.
