# TASK_018_customer_account_completion_and_self_service_hardening

Estado: done (pendiente validacion visual/manual final)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Agregar una capa minima de completion y operacion de cuenta/perfiles en `/auth/login?mode=profile` y `/admin`: pedir correo al primer acceso, gestionar telefono/contrasena con OTP y habilitar estados `active|inactive` por perfil.

Alcance:
- Backend:
  - OTP para cambios autenticados de cuenta (`password` y `phone`).
  - endpoints protegidos de cuenta y perfiles.
  - update de perfil compatible por `POST|PUT|PATCH` en `/v1/auth/profiles/:profileId`.
  - OTP de cambio de telefono enviado al numero registrado actual.
- Web profile:
  - icono unico de edicion en header para abrir modal de cuenta.
  - sin botones separados de editar en usuario/telefono/contrasena.
  - modal unico con usuario, correo, telefono y contrasena.
  - telefono/contrasena via OTP en el mismo modal.
  - titulo de bloque `Perfiles` (sin texto `max 2`).
  - estado de cuenta junto al avatar (`demo|activa|expirada|inactiva` + vencimiento).
  - edicion de perfil (nombre/avatar) con fallback de metodo para evitar 405.

No tocar:
- Billing/pagos.
- Migracion a DB.
- RBAC complejo.
- TV app.

Archivos involucrados:
- `apps/web-app/public/auth/login.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `backend/src/accountChangeOtp.js`
- `backend/src/server.js`
- `docs/03_bugs/BUG_002_profile_edit_method_not_allowed.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
- `docs/02_tasks/TASK_018_customer_account_completion_and_self_service_hardening.md`

Implementacion realizada:
- Se quitan botones de edicion por campo en la grilla superior de cuenta.
- Se agrega lapiz unico en la cabecera del perfil para abrir el modal de edicion de cuenta.
- El modal de cuenta abre en vista unificada con:
  - `username` + `email`
  - cambio de `telefono` con OTP
  - cambio de `contrasena` con OTP
- Se renombra el bloque de perfiles de `Perfiles (max 2)` a `Perfiles`.
- Se agrega visual del estado de cuenta junto al avatar con etiqueta y vencimiento.
- Se corrige guardado de perfil/avatar:
  - frontend intenta `POST` y ante 405 reintenta `PUT`.
  - backend acepta `POST|PUT|PATCH` para update de perfil.
- Se ajusta OTP de cambio de telefono para enviarse al numero ya registrado.

Riesgos:
- El dato de vencimiento puede no estar disponible en todos los entornos; se muestra `Sin fecha` cuando falta.
- Validacion final depende de pruebas manuales con backend reiniciado y proveedor SMS activo.

Pendiente de prueba:
- Validar flujo de modal unico de cuenta en UI real.
- Validar cambio de telefono OTP recibido en numero registrado actual.
- Validar que editar avatar/nombre de perfil no arroje 405.
- Validar estado de cuenta mostrado junto al avatar para `trial|active|expired|suspended`.

Resultado esperado:
- UX de cuenta simplificada con un unico punto de edicion.
- Cambios sensibles protegidos por OTP.
- Edicion de perfil estable sin `Method Not Allowed`.

Pasos manuales si existen:
- Reiniciar backend para cargar cambios de metodos/rutas.
- Si se prueba OTP real, configurar `SMS_ZDSMS_*`.
- No hay cambios manuales en XUI para esta tarea.
