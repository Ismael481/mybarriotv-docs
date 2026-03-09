# CHATGPT_CONTEXT

Fecha: 2026-03-09  
Rama: `main`  
Tarea activa: `TASK_052_xui_playback_url_resolution_from_signed_playlist`

## Resumen operativo
- Diagnostico playback confirmado:
  - Home cargaba desde XUI, pero playback bridge devolvia URL `/live/...` no valida para este panel.
- Fix aplicado en backend:
  - playback xtream se resuelve desde playlist firmada (`/playlist/.../m3u`) y devuelve URL `/play/<token>/m3u8`.
  - `XUI_API_KEY` se deja vacio en runtime local para modo xtream.
- Ajuste runtime adicional:
  - `XUI_OUTPUT=ts` para devolver `.../play/<token>/ts` y evitar fallo de stream en TV en entorno actual.
- Resultado validado:
  - `GET /v1/content/home` y `GET /v1/content/31/playback` responden con datos operativos.

## Archivos clave del ajuste actual
- `backend/.env` (local, ignorado por git)
- `backend/src/xuiClient.js`
- `docs/02_tasks/TASK_052_xui_playback_url_resolution_from_signed_playlist.md`

## Validacion
- Validacion runtime:
  - `GET /v1/content/home` devuelve secciones/items de XUI.
  - `GET /v1/content/31/playback` devuelve URL firmada `/play/.../ts` (`streamType=mpegts`).
  - `npm test` backend OK (`6` pruebas, `0` fallos).

## Cambios manuales externos requeridos
- Ninguno fuera del repo.
- Si cambian credenciales de linea o host XUI, actualizar `XUI_*` en `backend/.env` y reiniciar backend.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_052_xui_playback_url_resolution_from_signed_playlist.md`
- `docs/03_bugs/BUG_021_xtream_live_url_not_playable_for_signed_panel_streams.md`
- `docs/03_bugs/BUG_015_sms_provider_not_configured_error_surface.md`
- `docs/03_bugs/BUG_016_zdsms_302_redirect_and_local_recipient_format.md`
- `docs/03_bugs/BUG_017_web_otp_reload_blank_locked_identity_fields.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
