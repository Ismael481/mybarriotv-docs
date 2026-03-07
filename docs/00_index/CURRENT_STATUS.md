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

## Riesgos actuales
- Store JSON no sustituye DB productiva.
- Validacion final pendiente con credenciales reales de `zdsms` en entorno usuario.
