# BUG_011_admin_expiry_state_stale_until_customer_login

Estado: fixed

Fecha de deteccion:
2026-03-08

Fecha de correccion:
2026-03-08

Severidad:
Alta (estado operacional incorrecto en admin)

Descripcion:
Cuentas vencidas seguian apareciendo `active` en panel admin hasta que el cliente intentaba login; solo entonces se actualizaba a `expired`.

Causa raiz:
- La transicion automatica por fecha vencida estaba acoplada al flujo de acceso/login (`buildAccessDecision`).
- Las lecturas ops (`list/get`) no reconciliaban expiracion.
- UI admin no tenia refresco automatico periodico.

Correccion aplicada:
- Backend: reconciliacion de expiracion agregada en metodos de lectura de cuentas/perfiles en `authPersistence`.
- Admin web: auto-refresh cada 15s de listado + detalle con pestaña visible.

Archivos:
- `backend/src/authPersistence.js`
- `apps/web-app/public/auth/admin.html`

Validacion pendiente:
- Prueba manual en entorno ops con cuenta que expire por tiempo sin login del cliente.
