# TASK_039_minimum_automated_test_foundation

Estado: completed

Fecha de creacion:
2026-03-09

Ultima actualizacion:
2026-03-09

Objetivo:
Crear una base minima de pruebas automatizadas para reducir riesgo de regresion en auth/access/ops y superficies web criticas, antes de modularizacion backend/web.

Alcance:
- Suite automatizada minima en backend usando `node:test`.
- Cobertura basica de contratos criticos ya existentes (sin nuevas features, sin cambios de arquitectura).
- Smoke liviano de web auth/admin servido por backend.
- Documentacion operativa y comandos de ejecucion.

Archivos tocados:
- `backend/package.json`
- `backend/test/minimum-foundation.test.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_039_minimum_automated_test_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se agrego comando `npm test` en backend.
- Se creo suite `backend/test/minimum-foundation.test.js` con pruebas reproducibles y aisladas usando:
  - puertos dinamicos
  - `AUTH_STORE_FILE` temporal por prueba
  - fixtures de cuentas `active|expired|suspended`
  - arranque real de backend por `child_process`
- Casos cubiertos:
  - backend falla al boot con `AUTH_JWT_SECRET` inseguro
  - backend arranca correctamente con secreto valido
  - `GET /v1/auth/access` para `active`, `expired`, `suspended`
  - validacion explicita de bloqueo runtime por `canAccessApp=false`
  - preflight `OPTIONS` para ruta ops con `DELETE`
  - smoke web auth/admin: `/auth/login`, `/admin`, `/auth/device-approve`, assets
  - contrato JSON base usado por web auth/admin: `/v1/auth/me` y `/v1/auth/ops/accounts`
- Se documento comando y limites de cobertura en `backend/README.md`.

Ejecucion minima validada:
- Comando ejecutado: `npm test` (en `backend/`).
- Resultado: `5` pruebas OK, `0` fallos.

Riesgos:
- Cobertura es base y contractual; no reemplaza pruebas E2E completas.
- Backend monolitico y JS inline extenso en web siguen siendo riesgo de mantenibilidad (no parte de esta tarea).

Pendiente de prueba:
- E2E navegador real para flujos UI completos.
- Instrumentation/UI tests Android TV en entorno con toolchain estable.

Resultado esperado alcanzado:
- Existe una suite minima automatizada ejecutable en entorno normal.
- Flujos criticos de auth/access/ops cubiertos a nivel basico.
- Validacion no depende de build Android local.

Cambios manuales externos:
- Ninguno en XUI.
- Ninguna configuracion externa requerida para ejecutar la suite minima local.
