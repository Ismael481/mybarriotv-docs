# TASK_049_trial_expiry_must_not_start_from_admin_or_web

Estado: completed

Fecha:
2026-03-09

## Objetivo
Corregir que cuentas `trial` no reciban `expiresAt` por acciones web/admin antes de iniciar sesion en TV.

## Alcance
- Backend `authPersistence` y `server` (ops status update).
- Test automatizado de regresion en backend.
- Sin cambios de arquitectura.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/test/minimum-foundation.test.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_049_trial_expiry_must_not_start_from_admin_or_web.md`
- `docs/03_bugs/BUG_018_trial_expiry_auto_set_from_ops_status.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Cuentas legacy con `trialStartedAt` valido mantienen su `expiresAt` (comportamiento esperado).
- Validacion manual requerida en entorno real para confirmar panel admin.

## Pendiente de prueba
- Flujo admin real:
  1. cuenta nueva en `trial` sin login TV,
  2. aplicar estado `trial` desde admin,
  3. confirmar que `expiresAt` sigue nulo.

## Resultado esperado
- `trial` sin inicio TV => `expiresAt=null`.
- Solo login TV (manual o QR exchange) inicia demo y define expiracion.

## Pasos manuales si existen
1. Crear cuenta nueva por web OTP.
2. Entrar a `/admin` y abrir detalle de esa cuenta.
3. Aplicar estado `trial`.
4. Confirmar en detalle y en `GET /v1/auth/me` que `account.expiresAt` sigue `null` hasta login TV.
