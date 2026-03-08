# TASK_028_register_otp_pending_lock_after_reload

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Evitar que el flujo de OTP de registro pueda reiniciarse al recargar la pagina y bloquear solicitudes duplicadas mientras exista un OTP pendiente/no completado.

Alcance:
- Backend registro OTP:
  - detectar solicitud abierta de registro (`pending|verified`, no expirada) por `phone/username`.
  - rechazar nueva solicitud con `OTP_REQUEST_PENDING` y devolver metadata (`otpRequestId`, `expiresAtEpochSeconds`).
- API de registro:
  - propagar metadata de OTP pendiente en respuesta de error de `POST /v1/auth/register/request-otp`.
- Frontend web auth:
  - restaurar estado OTP pendiente tras recarga.
  - bloquear inputs de identidad durante cooldown para impedir reenvio/cambio de datos en esa ventana.
  - limpiar estado OTP local al expirar o completar.

Archivos tocados:
- `backend/src/authPersistence.js`
- `backend/src/registrationOtp.js`
- `backend/src/server.js`
- `apps/web-app/public/auth/login.html`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_028_register_otp_pending_lock_after_reload.md`
- `docs/03_bugs/BUG_006_register_otp_pending_state_lost_on_reload.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- El bloqueo backend por solicitud abierta aplica por `phone/username`; no es un lock global por navegador.

Pendiente de prueba:
- Flujo registro:
  - solicitar OTP,
  - recargar pagina,
  - verificar countdown activo y campos bloqueados,
  - verificar que no se crea otra solicitud OTP para el mismo registro.
- Completar registro y confirmar que se libera el lock.
- Dejar expirar OTP y confirmar desbloqueo.

Resultado esperado:
- Un solo OTP activo por registro en curso; recarga no rompe la continuidad ni permite duplicados inmediatos.

Pasos manuales externos:
- Ninguno.
