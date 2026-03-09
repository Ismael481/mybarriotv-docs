# BUG_018_trial_expiry_auto_set_from_ops_status

Estado: fixed

Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Sintoma reportado
- En panel admin, cuentas `trial` sin login TV mostraban `expiresAt` y podian expirar antes de consumir demo real.

## Causa raiz
- `handleOpsAccountStatusUpdate` aplicaba fallback `ops_trial_expiry_default` cuando estado era `trial` y no se enviaba `expiresAt`.
- Esto inyectaba expiracion desde admin/web, violando la regla de inicio de demo solo en TV.

## Correccion aplicada
- Se removio el fallback de expiracion automatica en ruta ops de estado de cuenta.
- `normalizeAccountRecord` fuerza `expiresAt=null` para `trial` sin `trialStartedAt` ni `demoConsumedAt`.
- `updateAccountStatus` preserva esa regla al transicionar a `trial`.
- Se agrego test de regresion en backend.

## Archivos tocados
- `backend/src/server.js`
- `backend/src/authPersistence.js`
- `backend/test/minimum-foundation.test.js`
- `docs/03_bugs/BUG_018_trial_expiry_auto_set_from_ops_status.md`

## Validacion
- `npm test` backend OK (`6` pruebas, `0` fallos).
- Test nuevo:
  - `ops trial status update does not auto-assign expiresAt before first TV login`.

## Paso manual externo requerido
- Validar visualmente en `/admin` con cuenta trial nueva sin login TV.
