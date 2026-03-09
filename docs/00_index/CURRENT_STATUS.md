# CURRENT_STATUS

## Estado general actual (2026-03-09)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Flujo auth/access/ops: operativo.
- TASK_006 a TASK_040: implementadas segun historial documental.
- TASK_039 base minima de pruebas automatizadas: completada y activa (`npm test`).
- TASK_040 modularizacion incremental backend auth/ops: completada.

## Avance concreto TASK_040
- `backend/src/server.js` reduce monolitismo y delega:
  - rutas auth/ops/web auth a `backend/src/routes/authOpsRoutes.js`
  - decision de acceso a `backend/src/services/accessDecision.js`
  - autorizacion ops a `backend/src/services/opsAuthorization.js`
- Se mantienen contratos funcionales de TV/web/admin sin cambios de producto.

## Riesgos cerrados en TASK_040
- Menor acoplamiento de auth/ops dentro de `server.js`.
- Mayor trazabilidad para cambios incrementales futuros en rutas y servicios auth/ops.

## Riesgos abiertos actuales
- `server.js` sigue concentrando handlers y parte del flujo bridge.
- JS inline extenso en `apps/web-app/public/auth/login.html` y `admin.html`.
- Cobertura automatizada sigue siendo minima (sin E2E navegador ni Android instrumentado).

## Validaciones tecnicas TASK_040
- `npm test` en `backend/`: OK (`5` pruebas, `0` fallos).
- Contratos criticos protegidos por suite existente: login, access, preflight ops, smoke web/admin.

## Recomendacion de siguiente fase
1. Extraer incrementalmente handlers auth/ops desde `server.js` a modulos de controllers manteniendo contratos.
2. Añadir pruebas de contrato por modulo extraido (sin E2E pesado).
3. Luego modularizar JS web auth/admin en iteraciones pequenas.
