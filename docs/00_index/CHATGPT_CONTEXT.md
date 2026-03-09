# CHATGPT_CONTEXT

Fecha: 2026-03-08
Rama: `main`
Tarea activa: `TASK_038_security_and_config_hardening_foundation`

## Resumen operativo
- TASK_036 permanece cerrada y validada manualmente.
- TASK_038 aplicada: hardening tecnico minimo en seguridad/configuracion y operacion base.
- No se introdujeron features nuevas ni cambios de arquitectura.

## Hallazgos clave
- `backend/.env` removido del repo; `.env` locales ahora ignorados.
- `AUTH_JWT_SECRET` ahora es obligatorio y seguro (sin fallback default).
- CORS ops alineado con metodos reales (`DELETE` incluido en preflight).
- TV build ya no depende de `org.gradle.java.home` fijo dentro del repo.

## Pruebas tecnicas TASK_038
- `node --check` OK en `backend/src/auth.js` y `backend/src/server.js`.
- Validado que `buildAuthConfig()` falla sin secreto seguro.
- Validado arranque de backend con secreto valido y preflight `OPTIONS` para `DELETE` (status `204`).
- Build TV en este entorno sigue afectada por `JAVA_HOME` global externo, no por configuracion fija en repo.

## Cambios manuales externos
- Ninguno en XUI.
- Recomendado fuera del repo: rotar credenciales historicas usadas en `.env` local previo.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry.md`
- `docs/02_tasks/TASK_038_security_and_config_hardening_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
