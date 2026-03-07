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
- Nuevo modulo OTP reset password:
  - `backend/src/passwordResetOtp.js`
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
- Ultimo micro-ajuste: bloqueo de input codigo hasta `Enviar SMS` y boton `Registrar cuenta` habilitado solo cuando ya hay SMS solicitado y codigo escrito.
- Ultima correccion funcional: login por telefono ahora normaliza formatos (`XXXXXXXX`, `53XXXXXXXX`, `+53XXXXXXXX`) para evitar fallos post-registro.
- Se agrega flujo de restablecimiento de contrasena por SMS OTP (request/verify/complete) y link desde login web.
- Registro web ahora sugiere `Restablecer contrasena` con link directo cuando el telefono ya existe.
- Ajuste final: link de reset tambien en `USERNAME_EXISTS` y mitigacion de autocompletado inicial en login/registro.
- Ajuste final responsive: compactacion de componentes en mobile para evitar scroll vertical inicial.
- Ajuste puntual adicional: alineacion/centrado de titulo en mobile para modo `Restablecer contrasena`.
- Ajuste de copy: reemplazo de `contrasena` por `contraseña` en textos visibles de login/registro/reset.
- Flujo QR actualizado: `backend/src/deviceLogin.js` genera `qrUrl` hacia `/auth/login?mode=device-approve...`; `auth/device.html` queda como redirect legado.
- Limpieza legado web: se eliminan archivos web antiguos (`device.html`, `register.html`, `assets/auth.css`) y backend redirige `/auth/device` y `/auth/register` a modos de `/auth/login`.
- Ajuste UX flujo QR: aprobacion de TV pasa a automatica tras login en modo `device-approve`; web cambia a vista perfil minima post-login.
- Fix critico web auth: `login.html` vuelve a inyectar `Authorization: Bearer` en `api(...)` para endpoints protegidos.
- Ajuste de demo para pruebas: `AUTH_ACCOUNT_DEMO_TTL_SECONDS` (default 60s) aplica a tokens de cuentas registradas (`account:*`).
- TV app ahora guarda `expiresAt` recibido del backend en login manual/QR y ejecuta auto-logout local al expirar.
- Web auth en modo `profile` oculta paneles de marketing/registro para dejar una vista limpia de sesion activa.
- Hotfix web auth: `login.html` renombra variable global local (`container` -> `authContainer`) para evitar choque con `v34-mode.js`; se restablecen handlers de botones/formularios en flujo QR y login web.
- TV UX demo: `UserSession` persiste aviso one-shot al expirar demo y `LoginViewModel` lo muestra al regresar al login.

## Archivos clave modificados
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js`
- `backend/src/passwordResetOtp.js`
- `backend/src/smsProvider.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Variables nuevas principales
- OTP:
  - `AUTH_OTP_*`
  - `AUTH_RL_OTP_*`
  - `AUTH_RL_PW_RESET_*`
- SMS:
  - `SMS_ZDSMS_*`
  - `SMS_TIMEOUT_MS`

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
