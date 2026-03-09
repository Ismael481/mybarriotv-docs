# CURRENT_STATUS

## Estado general actual (2026-03-09)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Backend auth/ops modularizado incrementalmente (TASK_040): operativo.
- Web auth/admin modularizado incrementalmente (TASK_041 + TASK_042): operativo.
- Base de pruebas automatizadas minima (TASK_039): activa y estable.

## Avance concreto TASK_042
- Nuevos modulos internos web:
  - `auth/assets/login-helpers.js`
  - `auth/assets/login-state.js`
  - `auth/assets/login-render.js`
  - `auth/assets/admin-helpers.js`
  - `auth/assets/admin-api.js`
  - `auth/assets/admin-render.js`
- `login.app.js` y `admin.app.js` consumen modulos internos por responsabilidad y quedan enfocados en orquestacion de flujo/eventos.
- `login.html` y `admin.html` se mantienen limpios (markup + carga de assets).
- Smoke contractual extendido para validar:
  - carga de assets JS modularizados adicionales (`login-state`, `login-render`, `admin-api`, `admin-render`)
  - rutas ops principales consumidas por admin (`list`, `detail`, `status`, `expiry`, `device status`, `profile status`)
  - contrato base consumido por login en `/v1/auth/devices`

## Riesgos cerrados en TASK_042
- Menor acoplamiento interno en JS web auth/admin.
- Mejor trazabilidad para refactor incremental posterior por dominios de estado/render/api.

## Riesgos abiertos actuales
- `login.app.js` y `admin.app.js` aun mantienen tamano alto para mantenimiento a largo plazo.
- Cobertura sigue en nivel smoke/contrato (sin E2E navegador pesado ni Android instrumentado).

## Validaciones tecnicas TASK_042
- `npm test` en `backend/`: OK (`5` pruebas, `0` fallos).
- Sin regresiones detectadas en login, OTP, admin, cuentas, dispositivos y perfiles.

## Recomendacion de siguiente fase
1. Particionar handlers/eventos de `login.app.js` por dominio OTP/profile/ops-lite.
2. Particionar acciones de `admin.app.js` en modulo `admin-actions` manteniendo `admin-api` actual.
3. Mantener cambios chicos y reversibles sin alterar contratos producto.
