# BUG_004_admin_demo_settings_method_and_expiry_flow

Estado: fixed

Fecha:
2026-03-08

Contexto:
- En `/admin`, al guardar configuracion de demo global aparecia `Method Not Allowed`.
- En actualizacion de estado de cuenta, `accountStatus` podia mutar antes de validar `expiresAt`, dejando inconsistencia si fecha era invalida.
- Se requeria que demo expirado pasara y quedara persistido en `expired` hasta activacion de plan.

Impacto:
- Operador no podia confiar en el guardado de demo global.
- Riesgo de estado parcial al actualizar cuenta con fecha invalida.
- Cuenta podia quedar visualmente desalineada con su expiracion real.

Causa raiz:
- Endpoint `/v1/auth/ops/settings/demo` con soporte estricto de metodo en algunos entornos cliente/proxy.
- Orden de validacion/aplicacion en `handleOpsAccountStatusUpdate` no era defensivo.
- Caducidad por fecha no persistia `accountStatus=expired` para todos los estados vencidos.

Correccion aplicada:
- Backend:
  - `/v1/auth/ops/settings/demo` acepta `POST|PUT|PATCH`.
  - CORS actualizado para `PUT` y `PATCH`.
  - `handleOpsDemoSettingsUpdate` acepta payload por unidad (`trialDurationValue` + `trialDurationUnit` minutes/hours).
  - `handleOpsAccountStatusUpdate` valida `expiresAt` antes de mutar estado.
  - `buildAccessDecision` persiste `expired` al vencer por fecha:
    - `trial` -> `consumeAccountDemo(...)` (marca demo consumido y expira cuenta).
    - `active` -> `updateAccountStatus(..., "expired", ...)`.
- Admin web:
  - Demo global configurable por minutos/horas.
  - Fallback de guardado (`POST` y si falla por metodo, `PUT`).
  - Mejor UX para expiracion de cuenta (rapida por duracion, aplicar demo global, limpiar y preview).

Archivos:
- `backend/src/server.js`
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/v34-custom.css`

Prueba minima pendiente:
- Guardar demo global en minutos/horas y recargar admin para confirmar persistencia.
- Crear cuenta trial nueva y verificar `expiresAt` segun demo global.
- Esperar vencimiento demo y validar que cuenta quede en `expired` persistido.
- Intentar actualizar cuenta con fecha invalida y confirmar que no cambie estado.
