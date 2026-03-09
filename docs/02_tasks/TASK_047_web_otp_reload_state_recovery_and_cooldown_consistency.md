# TASK_047_web_otp_reload_state_recovery_and_cooldown_consistency

Estado: completed

Fecha:
2026-03-09

## Objetivo
Corregir el comportamiento de recarga en `/auth/login` para flujos OTP (registro/reset), evitando campos bloqueados en blanco y manteniendo cooldown consistente.

## Alcance
- Ajuste frontend web auth en estado OTP y rehidratacion UI tras reload.
- Sin cambios de contrato API backend.
- Sin cambios de arquitectura.

## Archivos tocados
- `apps/web-app/public/auth/assets/login-state.js`
- `apps/web-app/public/auth/assets/login-handlers.js`
- `apps/web-app/public/auth/assets/login.app.js`
- `apps/web-app/public/auth/assets/login-profile-ui.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_047_web_otp_reload_state_recovery_and_cooldown_consistency.md`
- `docs/03_bugs/BUG_017_web_otp_reload_blank_locked_identity_fields.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Se usa almacenamiento local para rehidratacion de identidad basica (`username/phone`) mientras OTP esta pendiente.
- Requiere validacion manual en navegador real para confirmar UX final en ambos flujos.

## Pendiente de prueba
- Recargar durante OTP activo en `mode=signup` y `mode=reset`.
- Verificar que:
  - inputs identidad se ven con datos restaurados y bloqueados,
  - input OTP sigue editable,
  - al expirar countdown se desbloquea y habilita nuevo request OTP.

## Resultado esperado
- Reload no deja formulario en estado inconsistente.
- Cooldown de 5 minutos se respeta tras recarga.
- Usuario no puede pedir nuevo OTP antes de expirar el vigente.

## Pasos manuales (si existen)
1. Abrir `/auth/login?mode=reset` o `/auth/login?mode=signup`.
2. Solicitar OTP.
3. Recargar pagina.
4. Confirmar que telefono/usuario no quedan en blanco y solo OTP es editable.
5. Esperar expiracion y confirmar que se habilita nuevo `Enviar SMS`.
