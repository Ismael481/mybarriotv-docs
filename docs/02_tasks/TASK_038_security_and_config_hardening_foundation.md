# TASK_038_security_and_config_hardening_foundation

Estado: completed

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Aplicar hardening tecnico minimo de seguridad/configuracion y portabilidad antes de nuevas tareas funcionales.

Alcance:
- Seguridad y configuracion de backend.
- Consistencia operativa CORS para metodos ops reales.
- Portabilidad minima de build TV (sin dependencia local fija en Gradle).
- Actualizacion documental completa.

Archivos tocados:
- `.gitignore`
- `backend/.env` (eliminado del repo)
- `backend/.env.example`
- `backend/src/auth.js`
- `backend/src/server.js`
- `backend/README.md`
- `apps/tv-app/gradle.properties`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_038_security_and_config_hardening_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- Se elimino `backend/.env` versionado y se agrego ignore explicito para `.env` locales.
- Se dejo `.env.example` como referencia unica versionada.
- Se elimino fallback inseguro de `AUTH_JWT_SECRET` y ahora `buildAuthConfig()` falla si el secreto falta o es default/inseguro.
- Se endurecio bootstrap de backend para fallar explicita y claramente al inicio si la configuracion auth es invalida.
- Se normalizo carga de `.env` para dos rutas esperables (`process.cwd()` y `backend/.env`).
- Se corrigio CORS permitiendo `DELETE` en preflight (`Access-Control-Allow-Methods`).
- Se removio `org.gradle.java.home` fijo de `apps/tv-app/gradle.properties`.
- Se actualizo README backend con regla de `.env` no versionado y secreto JWT obligatorio.

Validaciones ejecutadas:
- `node --check backend/src/auth.js` OK.
- `node --check backend/src/server.js` OK.
- Validacion de config auth:
  - sin secreto: falla con mensaje claro (`Missing secure AUTH_JWT_SECRET...`).
  - con secreto fuerte: config auth valida.
- Preflight CORS para ops con `DELETE`: `204` y header `Access-Control-Allow-Methods` incluye `DELETE`.
- Backend boot con env valido: arranca correctamente (validado en puerto 8099).
- Gradle TV: la referencia fija local fue removida; en este entorno sigue fallando por `JAVA_HOME` global externo al repo.

Riesgos / notas:
- Se requiere definir `AUTH_JWT_SECRET` fuerte por entorno para arrancar backend.
- Para build TV, el entorno debe tener JDK/JBR valido (`JAVA_HOME` correcto o Java en PATH).

Cambios manuales externos:
- Ninguno en XUI.
- Recomendado fuera del repo: rotacion de credenciales previamente expuestas en `.env` local historico.
