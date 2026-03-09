# TASK_051_xui_runtime_env_completion_and_loader_precedence_fix

Estado: completed

Fecha:
2026-03-09

## Objetivo
Dejar operativo el bridge de contenido XUI con configuracion runtime real y corregir precedencia de carga `.env`.

## Alcance
- Completar variables `XUI_*` en `backend/.env` local.
- Ajustar loader de entorno en backend para no ignorar valores de `.env` cuando el key existe vacio.
- Validar endpoint de catalogo bridge.

## Archivos tocados
- `backend/.env` (local, ignorado por git)
- `backend/src/server.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_051_xui_runtime_env_completion_and_loader_precedence_fix.md`
- `docs/03_bugs/BUG_020_env_loader_ignored_dotenv_when_env_key_present_empty.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Credenciales de linea XUI pueden cambiar y requerir ajuste manual en `.env`.
- `http` en lugar de `https` depende de configuracion TLS del host panel.

## Pendiente de prueba
- Validar playback puntual (`/v1/content/:id/playback`) desde app TV con un `stream_id` real.

## Resultado esperado
- `GET /v1/content/home` devuelve catalogo real de XUI en backend local.

## Pasos manuales si existen
1. Verificar `.env` con bloque `XUI_*`.
2. Reiniciar backend.
3. Probar `GET /v1/content/home`.
4. Probar flujo Home/Playback en TV.
