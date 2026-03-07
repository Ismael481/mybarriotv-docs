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
  - `POST /v1/auth/password-reset/request-otp`
  - `POST /v1/auth/password-reset/verify-otp`
  - `POST /v1/auth/password-reset/complete`
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
  - regla UX final de registro: `Registrar cuenta` queda deshabilitado hasta enviar el primer SMS y escribir codigo; input de codigo queda bloqueado hasta solicitar SMS
  - correccion funcional login: backend ahora acepta telefono de login en variantes `XXXXXXXX`, `53XXXXXXXX` y `+53XXXXXXXX` para evitar `Invalid credentials` por formato
  - login web ahora muestra link `Restablecer contrasena` bajo botones de acceso
  - flujo visual de restablecimiento en la misma pantalla (modo reset): telefono + nueva contrasena + confirmar contrasena + SMS code
  - seguridad OTP equivalente al registro: request/verify/complete con expiracion, intentos maximos y rate limiting
  - ajuste UX adicional en registro: si el telefono ya existe (`PHONE_EXISTS`), se muestra mensaje claro y link pequeno para llevar directo al modo `Restablecer contrasena`
  - ajuste UX adicional: el link pequeno a `Restablecer contrasena` se activa tambien en `USERNAME_EXISTS`
  - ajuste de entrada web: desactivacion/limpieza de autocompletado al cargar para evitar campos prellenados automaticamente
  - ajuste responsive final: compactacion de escala en breakpoints moviles para evitar scroll vertical inicial (sin cambiar UX/flujo)
  - ajuste puntual mobile adicional: en modo `Restablecer contrasena` se corrige centrado de titulo y salto de linea visual
  - ajuste de copy final: textos visibles de web auth actualizados para usar `contraseÃ±a` con `Ã±`
  - correccion de flujo QR web: el `qrUrl` ahora apunta a `/auth/login?mode=device-approve&sessionId=...` (superficie web nueva)
  - compatibilidad retroactiva: backend redirige `/auth/device` y `/auth/register` a modos de `/auth/login`
  - limpieza de codigo legado web: eliminados `apps/web-app/public/auth/device.html`, `apps/web-app/public/auth/register.html` y `apps/web-app/public/auth/assets/auth.css`
  - flujo login ajustado: luego de iniciar sesion web se redirige a vista minima de perfil
  - modo QR `device-approve`: tras login web, la aprobacion de TV se ejecuta automaticamente y luego se abre perfil (sin botones manuales `Aprobar/Denegar`)
  - perfil web minimo: usuario, telefono (si aplica), placeholder de contraseÃ±a y lista de TVs vinculadas
  - fix de autorizacion web: llamadas protegidas del frontend incluyen nuevamente `Authorization: Bearer`, corrigiendo `Unauthorized` en `device/approve`
  - mensaje SMS actualizado a "codigo de verificacion" (sin termino OTP visible al cliente)
  - ajuste de perfil web: al abrir `mode=profile` se ocultan paneles `Registrarse/Entrar` para evitar UI mezclada con sesion activa
  - countdown de demo en perfil ajustado para reflejar TTL corto de cuenta cuando se configura `AUTH_ACCOUNT_DEMO_TTL_SECONDS`
  - hotfix de interaccion web: corregido choque de variable global `container` con `v34-mode.js`; se restablece funcionamiento de `Entrar`, `Registrarse` y `Restablecer Contraseña` sin recarga silenciosa

### TV app
- Sin cambios de arquitectura ni rediseÃ±o.
- Login manual y QR se mantienen funcionales.
- Login manual y QR ahora guardan `expiresAtEpochSeconds` en `UserSession`.
- `DefaultUserSession` agrega auto-logout al expirar token para regresar automaticamente a login.
- Nuevo aviso post-expiracion: al volver al login muestra mensaje `Tu demo ha expirado. Activa una suscripción para continuar.`
- Ajuste visual login TV: layout dual en la misma vista (QR a la izquierda, login manual a la derecha), sin cambios de arquitectura.
- Se elimina boton `Regenerar QR`; la regeneracion ahora es automatica al detectar estados `expired` o `denied`.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js` (nuevo)
- `backend/src/smsProvider.js` (nuevo)
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/tv-app/libs/auth-imp/src/main/kotlin/com/techlads/auth/imp/DefaultUserSession.kt`
- `apps/tv-app/libs/auth/src/main/kotlin/com/techlads/auth/UserSession.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreen.kt`
- `apps/tv-app/features/login/src/main/java/com/techlads/login/withEmailPassword/LoginScreenContent.kt`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/navigation/AppNavigation.kt`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Variables de entorno requeridas
- OTP/Auth:
  - `AUTH_ACCOUNT_DEMO_TTL_SECONDS` (TTL corto de demo para cuentas registradas)
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

## Pruebas realizadas (2026-03-07)
- Backend:
  - `POST /v1/auth/login` con credenciales invalidas devuelve `401`.
  - `POST /v1/auth/device/approve` validado con bearer token real y `sessionId` valido.
  - verificado TTL diferenciado de token:
    - cuentas `account:*` usan `AUTH_ACCOUNT_DEMO_TTL_SECONDS`.
    - usuarios demo `user:*` mantienen `AUTH_TOKEN_TTL_SECONDS`.
- Web:
  - flujo `device-approve` corrige `Missing bearer token` tras fix de header.
  - fix de JS global en login (`container`) recupera funcionamiento de botones y submit.
- TV:
  - compilacion `:app:compileDebugKotlin` en verde.
  - login dual actualizado (QR izquierda / manual derecha).
  - QR sin boton de regeneracion manual; reintento automatico en `expired`/`denied`.
  - auto-logout por expiracion demo con aviso visible en login.

## Pendiente antes de perfiles
- Ajustar politicas productivas de cuenta (bloqueo, recuperacion, etc.).
- Migrar persistencia de cuentas/otp a DB productiva.
- Endurecer controles anti-abuso y observabilidad avanzada.
