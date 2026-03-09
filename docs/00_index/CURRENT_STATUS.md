# CURRENT_STATUS

## Estado general actual (2026-03-09)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Backend auth/ops modularizado incrementalmente (TASK_040): operativo.
- Web auth/admin modularizado incrementalmente (TASK_041..TASK_044): operativo.
- Usabilidad operativa admin (TASK_043): operativa y estable.
- Base de pruebas automatizadas minima (TASK_039): activa y estable.

## Avance concreto TASK_044
- Login web:
  - Nuevo modulo `auth/assets/login-handlers.js` separado por dominio (`otp`, `profile`, `ops-lite`).
  - `login.app.js` mas pequeno, centrado en estado/render/wiring de eventos.
- Admin web:
  - Nuevo modulo `auth/assets/admin-actions.js` para acciones operativas principales.
  - `admin.app.js` mas pequeno y enfocado en inicializacion/eventos de alto nivel.
- Hotfix de codificacion UI:
  - Corregidos textos/simbolos mojibake en tarjetas de perfiles web (`lapiz` y separador de avatar/id).
  - Mensajes de `Contrasena` normalizados para evitar caracteres corruptos.
- Contratos backend preservados (sin cambios de negocio).

## Riesgos cerrados en TASK_044
- Menor acoplamiento de handlers/eventos en login web.
- Menor acoplamiento de acciones operativas en admin web.
- Corregida confusion visual por caracteres de codificacion en perfiles.

## Riesgos abiertos actuales
- Cobertura se mantiene en nivel smoke/contrato (sin E2E navegador completo).
- Persisten limites de alcance global (sin DB productiva, sin billing, sin RBAC completo), fuera de esta tarea.

## Validaciones tecnicas TASK_044
- `npm test` en `backend/`: OK (`5` pruebas, `0` fallos).
- Smoke extendido para assets `login-handlers.js` y `admin-actions.js`.

## Recomendacion de siguiente fase
1. Si hace falta, dividir `login-handlers` por archivos de dominio sin tocar contratos.
2. Mantener particiones pequenas y reversibles en web/admin sin mezclar arquitectura.
3. Continuar sincronizando indices en cada cierre de tarea.
