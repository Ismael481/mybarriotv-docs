# ACTIVE_TASK

Tarea activa: `TASK_060_xui_ops_provisioning_hardening`

Estado:
- `in_progress` (inicio controlado en modo documental/diseno minimo).
- No hay otras tareas activas.

Objetivo inmediato:
- Endurecer de forma minima el flujo existente de provisioning `create+link` con XUI, sin cambiar la arquitectura general ni romper el bridge validado.

Alcance acotado:
- Compatibilidad minima con bouquets/paquetes reales.
- Manejo de diferencias de parametros entre paneles XUI.
- Mejor mapeo de errores operativos en provisioning.
- Registro del pendiente de rotacion de credenciales/API key usadas en pruebas.

Progreso de validacion:
- Ejecutado: provisioning exitoso + error controlado (`XUI_ADMIN_ACTION_FAILED`).
- Pendiente de cierre: endurecer/definir validacion de bouquets reales (se detecto alta exitosa con bouquetId invalido).

Referencias cerradas:
- `TASK_057_playback_failover_y_hardening_audio_stream`: completed.
- `BUG_022_tv_stream_cortes_y_audio_intermitente_en_playback_xui`: closed.
