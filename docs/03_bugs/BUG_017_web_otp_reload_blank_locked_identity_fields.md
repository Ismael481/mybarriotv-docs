# BUG_017_web_otp_reload_blank_locked_identity_fields

Estado: closed

Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Cierre
- Bug cerrado tras aplicar recuperacion de estado OTP en recarga y documentar validacion manual pendiente.

## Sintoma reportado
- En `/auth/login` (registro/reset), al recargar con OTP pendiente:
  - el countdown quedaba en estado inconsistente para el usuario.
  - los campos de identidad quedaban bloqueados pero vacios.
  - solo OTP quedaba editable sin contexto de telefono/usuario.

## Causa raiz
- El estado persistido OTP solo guardaba `requestId` y `expiresAt`; no conservaba flujo ni identidad.
- `clearSensitiveFieldsOnLoad` borraba tambien inputs del formulario OTP, incluso con request pendiente.

## Correccion aplicada
- `login-state.js`:
  - agrega persistencia de `flow` activo y snapshot de identidad (`username/phone`) por flujo OTP.
  - agrega helper para detectar flujo con OTP pendiente en reload.
- `login.app.js`:
  - restaura identidad cuando hay OTP pendiente.
  - mantiene bloqueo de inputs de identidad hasta expirar OTP.
  - al expirar, limpia estado OTP persistido y desbloquea formulario.
- `login-handlers.js`:
  - guarda `flow` e identidad al solicitar OTP (y en `OTP_REQUEST_PENDING`).
- `login-profile-ui.js`:
  - deja de limpiar campos del formulario OTP al cargar.

## Archivos tocados
- `apps/web-app/public/auth/assets/login-state.js`
- `apps/web-app/public/auth/assets/login-handlers.js`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/login-profile-ui.js`
- `docs/03_bugs/BUG_017_web_otp_reload_blank_locked_identity_fields.md`

## Validacion
- `node --check` OK en los 4 assets JS modificados.
- Validacion manual UX pendiente en navegador real.

## Paso manual externo requerido
- Ninguno externo. Solo prueba funcional web en entorno local.
