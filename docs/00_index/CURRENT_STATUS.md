# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` operativo.
- `TASK_006` implementada.
- `TASK_007` cerrada como diseno.
- `TASK_008` implementada.
- `TASK_009` implementada y validada.
- `TASK_010` implementada (web auth surface formalizada en `apps/web-app`).
- `TASK_011` implementada (OTP SMS + registro real), pendiente validacion final con proveedor real.

## Auth actual
### Core
- `POST /v1/auth/login`
- `GET /v1/auth/me`
- `GET /v1/auth/protected`

### Registro OTP SMS
- `POST /v1/auth/register/request-otp`
- `POST /v1/auth/register/verify-otp`
- `POST /v1/auth/register/complete`

### Password reset OTP SMS
- `POST /v1/auth/password-reset/request-otp`
- `POST /v1/auth/password-reset/verify-otp`
- `POST /v1/auth/password-reset/complete`

### Device login QR
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`

### Device binding
- `GET /v1/auth/devices`
- `POST /v1/auth/devices/revoke`
- Estado: `active` / `revoked`

## Persistencia y hardening
- Store JSON para cuentas, OTP requests, sesiones QR y dispositivos.
- Rate limiting basico en QR + endpoints OTP.
- Auditoria basica de eventos auth/device/otp.

## Web auth
- Vive en `apps/web-app/public/auth`:
  - `/auth/login`
  - `/auth/device`
  - `/auth/register`
- `register.html` ya conectado al flujo OTP real.
- `/auth/login` usa layout visual adaptado de `login-form-v34`.
- Flujo UX de registro endurecido: validaciones fuertes, countdown OTP y bloqueo de campos tras envio OTP.
- Ultimo ajuste UX: alineacion fina de botones/input SMS, mejor visibilidad del icono revelar contrasena y mensajes de error orientados a cliente (sin codigos tecnicos).
- Micro-ajuste visual final aplicado: input codigo SMS reducido y boton `Enviar SMS` ampliado para alineacion mas limpia en desktop.
- Ajuste final aplicado: alineacion exacta de la fila SMS usando grid (mismo ancho visual que inputs superiores) y ojo de password sin fondo circular.
- Ajuste puntual adicional: ojo de password reposicionado para visibilidad estable, `Registrar cuenta` al ancho del input y campo telefono reajustado visualmente.
- Ajuste final puntual: placeholder telefono unificado a `Telefono +53XXXXXXXX` y ojito de password con icono nativo visible.
- Ajuste UX adicional: placeholder del campo password simplificado a `Contrasena` con aviso en blur si no cumple politica fuerte.
- Ajuste UX adicional: aviso en blur del telefono cuando no cumple formato esperado.
- Ajuste UX adicional: boton `Registrar cuenta` deshabilitado hasta SMS solicitado + codigo escrito; campo codigo bloqueado hasta envio SMS.
- Correccion backend login: autenticacion por telefono robusta frente a formato (8 digitos / 53 / +53).
- Nuevo flujo web de restablecimiento de contrasena por SMS OTP desde `/auth/login` (link bajo botones de login).
- En registro, `PHONE_EXISTS` ahora muestra recomendacion con acceso directo a `Restablecer contrasena`.
- El acceso directo a `Restablecer contrasena` en registro aplica para `PHONE_EXISTS` y `USERNAME_EXISTS`.
- Ajuste de UX web para reducir autocompletado inicial no deseado en campos sensibles.
- Ajuste responsive movil aplicado para minimizar scroll vertical inicial sin alterar la estructura visual.
- Ajuste puntual mobile en modo `Restablecer contrasena`: centrado del titulo y correccion de salto de linea visual.
- Ajuste de textos UI: uso de `contraseña` con `ñ` en campos y mensajes web auth.
- Flujo QR web unificado: QR abre login web nueva en modo `device-approve`; ruta legacy `/auth/device` solo redirige.
- Limpieza de legado web auth completada: removidos `auth/device.html`, `auth/register.html` y `assets/auth.css`; queda una sola superficie (`/auth/login`) con rutas legacy por redireccion backend.
- Login web ahora redirige a perfil minimo post-login; en modo QR la aprobacion TV es automatica al autenticarse.
- Corregido bug de autorizacion web: llamadas protegidas vuelven a incluir `Bearer token` desde frontend.
- Ajuste demo actual: cuentas registradas (`account:*`) usan TTL corto configurable por `AUTH_ACCOUNT_DEMO_TTL_SECONDS` (default 60s) para pruebas de expiracion.
- TV app: se persiste `expiresAt` al hacer login manual/QR y `UserSession` ahora ejecuta auto-logout al vencer token.
- Web profile: al entrar en modo `profile` se oculta el panel de `Registrarse` para evitar mezcla de estados autenticado/no autenticado.
- Hotfix web auth (2026-03-07): se corrige conflicto JS por redeclaracion global de `container` en `login.html`, que estaba rompiendo handlers (`Entrar`, `Registrarse`, `Restablecer Contraseña`) y provocando recarga sin mensajes.
- TV app: al expirar token demo se guarda aviso one-shot y el login muestra mensaje de `demo expirada` solicitando activar suscripcion.

## Riesgos actuales
- Store JSON no sustituye DB productiva.
- Validacion final pendiente con credenciales reales de `zdsms` en entorno usuario.
