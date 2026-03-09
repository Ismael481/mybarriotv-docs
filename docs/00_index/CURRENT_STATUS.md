# CURRENT_STATUS

## Estado general actual (2026-03-09)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Auth/access/ops backend: operativo y modularizado incrementalmente (TASK_040).
- Frontend web auth/admin: JS modularizado incrementalmente (TASK_041).
- TASK_039 base minima de pruebas automatizadas: activa y estable.

## Avance concreto TASK_041
- JS inline removido de:
  - `apps/web-app/public/auth/login.html`
  - `apps/web-app/public/auth/admin.html`
- Logica movida a:
  - `apps/web-app/public/auth/assets/login.app.js`
  - `apps/web-app/public/auth/assets/admin.app.js`
- Helper compartido agregado:
  - `apps/web-app/public/auth/assets/web-common.js` (`createApiClient`, `setLine`)
- Contratos backend auth/ops/admin preservados sin cambios funcionales.

## Riesgos cerrados en TASK_041
- Menor acoplamiento HTML+JS en superficies web criticas (`login/admin`).
- Mejor trazabilidad para siguientes refactors front incrementales.

## Riesgos abiertos actuales
- `login.app.js` y `admin.app.js` aun son extensos; falta particion interna adicional por dominio.
- Sin E2E navegador pesado; cobertura automatizada sigue en nivel smoke/contrato.

## Validaciones tecnicas TASK_041
- `npm test` en `backend/`: OK (`5` pruebas, `0` fallos).
- Smoke extendido valida carga de nuevos assets JS y referencias desde `/auth/login` y `/admin`.

## Recomendacion de siguiente fase
1. Particionar `login.app.js` en modulos internos (`state`, `render`, `handlers`, `profile`, `ops-lite`).
2. Particionar `admin.app.js` en modulos internos (`api`, `render`, `actions`, `events`).
3. Ampliar smoke contractual con casos adicionales de rutas ops principales.
