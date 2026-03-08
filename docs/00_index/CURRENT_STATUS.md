# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006 a TASK_019: implementadas (pendiente validacion manual final en algunos frentes UI/UX).
- TASK_020 (TV perfil post-login): implementada, pendiente validacion manual final.

## Auth backend vigente
- Core auth y cuenta: operativo.
- Device login QR:
  - `POST /v1/auth/device/start`
  - `GET /v1/auth/device/status/:sessionId`
  - `POST /v1/auth/device/approve`
  - `POST /v1/auth/device/exchange`
- `GET /v1/auth/me` expone perfiles de cuenta para consumo en TV/web.

## TV app (estado relevante)
- Login manual y QR con access gate operativo.
- QR con auto-regeneracion en expiracion/errores recuperables.
- Nuevo flujo post-login:
  - si hay perfiles activos y no hay perfil seleccionado -> pantalla `WhoIsWatching`.
  - `WhoIsWatching` ahora usa perfiles reales de backend (nombre + avatar).
  - perfil seleccionado se persiste en sesion local.

## Riesgos abiertos
- Persistencia auth/profiles en DataStore/JSON local (sin DB productiva).
- Validacion manual final pendiente de TASK_016, TASK_017, TASK_018, TASK_019 y TASK_020.
- No se corrio build Android completa en este ciclo por problema local de JBR (`jvm.cfg`).
- OTP real depende de proveedor SMS y variables `SMS_ZDSMS_*`.
