# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI`: operativo.
- TASK_006 a TASK_036: implementadas.

## TV acceso en runtime
- TV ahora consulta `/v1/auth/access` en ciclo de sesion activa.
- Si `canAccessApp=false` (expirada/suspendida/bloqueada), cambia a `AccessBlocked` automaticamente.
- Se evita permanecer en Home cuando la cuenta ya figura `expired` en backend/admin.

## Riesgos abiertos
- Validacion manual final en dispositivo real para confirmar latencia de salida (ciclo ~10s).
