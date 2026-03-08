# BUG_007_admin_profile_create_route_mismatch

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (funcionalidad principal de admin no operativa)

Descripcion:
Desde Admin, `Agregar perfil` no guardaba el segundo perfil (`p2`) aunque se enviaba nombre/avatar correctamente.

Causa raiz:
El router backend para create profile en ops validaba `segments.length === 7`, pero la ruta real `POST /v1/auth/ops/accounts/:accountId/profiles` tiene 6 segmentos.

Correccion aplicada:
- `backend/src/server.js`: ajuste del matcher a `segments.length === 6` para enrutar al handler `handleOpsProfileCreate`.

Archivos:
- `backend/src/server.js`

Validacion pendiente:
- Prueba manual en `/admin` creando `p2` desde detalle de cuenta.
