# CHATGPT_CONTEXT

Fecha: 2026-03-08
Rama: `main`
Tarea activa: `TASK_037_comprehensive_project_audit_and_next_steps`

## Resumen operativo
- Se realizo auditoria integral de `apps/tv-app`, `apps/web-app`, `backend` y `docs`.
- Se consolidaron debilidades, riesgos y orden recomendado de continuacion.
- No se modifico logica de negocio en esta tarea; solo documentacion y estado real.
- TASK_036 queda cerrada por validacion manual real reportada por usuario.

## Hallazgos clave
- Riesgo critico de seguridad/configuracion: `backend/.env` versionado con secretos y fallback inseguro en `AUTH_JWT_SECRET`.
- Riesgo alto de mantenibilidad: `backend/src/server.js` monolitico (~2581 lineas) y UI web auth/admin con JS inline muy grande.
- Riesgo alto de calidad: sin pruebas automatizadas activas en TV/backend/web y build Android bloqueado por JBR local.

## Pruebas tecnicas ejecutadas
- `node --check` OK en backend (`server.js`, `authPersistence.js`, `registrationOtp.js`, `deviceLogin.js`, `xuiClient.js`).
- `gradlew tasks` falla en TV por configuracion local fija de JBR (`C:/Program Files/Android/Android Studio/jbr/lib/jvm.cfg`).
- Validacion manual funcional de TASK_036: cambio de cuenta a `expired/suspended` expulsa TV de Home y muestra `AccessBlocked` inmediatamente.

## Cambios manuales externos
- Ninguno en XUI.
- Recomendado fuera del repo: rotar credenciales actuales y sacar secretos de `backend/.env` versionado.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_036_tv_runtime_access_gate_enforcement_on_account_expiry.md`
- `docs/02_tasks/TASK_037_comprehensive_project_audit_and_next_steps.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
