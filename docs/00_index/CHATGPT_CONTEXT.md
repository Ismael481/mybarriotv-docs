# CHATGPT_CONTEXT

Fecha: 2026-03-07
Rama: `main`
Tarea activa: `TASK_011_sms_otp_registration_and_verification`

## Resumen operativo
- TASK_011 ya esta implementada en codigo (backend, web y TV).
- Flujo actual soporta:
  - registro real por SMS OTP,
  - reset de contrasena por SMS OTP,
  - login web,
  - aprobacion QR de TV,
  - login manual en TV,
  - auto-logout por expiracion de demo.

## Cambios clave recientes
- Backend:
  - TTL demo corto para cuentas registradas: `AUTH_ACCOUNT_DEMO_TTL_SECONDS`.
  - login por telefono normalizado (`XXXXXXXX`, `53XXXXXXXX`, `+53XXXXXXXX`).
- Web:
  - una sola superficie en `/auth/login`.
  - fix de `Bearer token` en llamadas protegidas.
  - fix de conflicto JS (`container` vs `v34-mode.js`).
- TV:
  - persistencia real de `expiresAt` en sesion.
  - auto-logout al expirar token + aviso de demo expirada.
  - layout login dual (QR izquierda / login derecha).
  - QR con regeneracion automatica (sin boton manual).

## Pruebas tecnicas ejecutadas
- Compilacion Kotlin TV: `:app:compileDebugKotlin` OK.
- Backend auth TTL: verificado (`account:*` corto, `user:*` normal).
- Endpoint protegido de approve con bearer: validado en PowerShell.

## Variables importantes (manual)
- `AUTH_WEB_BASE_URL=http://10.10.6.121:8080`
- `AUTH_CORS_ORIGIN=http://10.10.6.121:8080`
- `AUTH_ACCOUNT_DEMO_TTL_SECONDS=60`
- `SMS_ZDSMS_BASE_URL`, `SMS_ZDSMS_API_TOKEN` (o email/pass), `SMS_TIMEOUT_MS`

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_011_sms_otp_registration_and_verification.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
