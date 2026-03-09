# TASK_048_demo_timer_start_on_first_tv_login_only

Estado: completed

Fecha:
2026-03-09

## Objetivo
Hacer que el tiempo demo de cuentas `trial` comience solo cuando la cuenta inicia sesion por primera vez en TV.

## Alcance
- Ajuste backend en persistencia y login TV.
- Sin cambios de arquitectura global.
- Sin cambios de contrato incompatibles para clientes actuales.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_048_demo_timer_start_on_first_tv_login_only.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Cuentas trial antiguas ya creadas con `expiresAt` previo mantienen su comportamiento existente.
- Requiere validacion manual en TV real para confirmar UX completa del primer inicio.

## Pendiente de prueba
- Flujo manual en TV fisica:
  1. crear cuenta en web,
  2. confirmar que en backend sigue `expiresAt=null` antes de login TV,
  3. iniciar sesion en TV (manual o QR),
  4. confirmar que en ese momento se fija `trialStartedAt/expiresAt`.

## Resultado esperado
- Registro/web no consume demo.
- Primer login en TV inicia countdown demo.

## Pasos manuales si existen
1. Crear cuenta nueva via OTP web.
2. Revisar `GET /v1/auth/me` (con token web) y confirmar `account.expiresAt` nulo antes de login TV.
3. Iniciar sesion en TV.
4. Revisar nuevamente `GET /v1/auth/me` y confirmar `account.expiresAt` poblado.
