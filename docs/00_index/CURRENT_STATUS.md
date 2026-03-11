# CURRENT_STATUS

## Estado general (2026-03-11)
- `TASK_054_sincronizacion_indices_post_task_053`: completada.
- `TASK_055_xui_account_link_foundation`: sigue `in_progress` sin cambios en esta auditoria.
- Tarea activa actual: `TASK_056_auditoria_stream_cortes_audio_xui_player`.
- Hallazgos tecnicos iniciales de `TASK_056`:
  - XUI entrega `playbackUrl` validas para algunos canales (`302 -> 200`) y fallidas para otros (`302 -> 404`) con mismo flujo `/play/<token>/ts`.
  - En muestra real aparecen codecs mixtos de audio (`aac`, `ac3`, `mp2`); `ac3` puede producir video sin audio en ciertos TVs.
  - El player TV usa ExoPlayer con configuracion base (sin `trackSelector`/`loadControl`/telemetria por codec), lo que limita resiliencia y diagnostico.

## Pendientes de prueba manual
- Validar en TV fisica un set minimo de canales:
  - 1 canal estable con audio AAC.
  - 1 canal con audio AC3.
  - 1 canal que actualmente termina en `404` tras redireccion de XUI.
- Capturar logs de player en el instante de "video sin audio" para confirmar codec/pista seleccionada.
- Revalidar smoke de `TASK_055` al retomar esa tarea.

## Riesgo/pendiente externo
- Hay dependencia externa de XUI: algunos streams requieren correccion operativa en panel/origen porque la URL firmada termina en `404`.
- Puede requerirse cambio manual externo en XUI para normalizar codec de audio (priorizar AAC en canales problemáticos).
- No se aplicaron cambios de codigo en esta auditoria; la incidencia de reproduccion sigue abierta.
