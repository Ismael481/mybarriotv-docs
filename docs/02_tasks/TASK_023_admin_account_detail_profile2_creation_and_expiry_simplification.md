# TASK_023_admin_account_detail_profile2_creation_and_expiry_simplification

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Simplificar el detalle de cuenta en Admin para operar estado/expiracion con un flujo directo, quitar controles de demo/expiracion rapida y permitir crear el segundo perfil directamente desde la seccion `Perfiles de cuenta`.

Alcance:
- Admin UI:
  - Boton de estado renombrado a `Aplicar`.
  - Se elimina `Demo por defecto`, `Usar demo global` y `Expiracion rapida` del detalle de cuenta.
  - Se mantiene una sola expiracion por `datetime-local` (fecha + hora) y aplicacion junto al estado.
  - Se elimina checkbox de `Segundo perfil pagado`.
  - Se agrega bloque `Agregar perfil` en `Perfiles de cuenta` para crear `p2` desde Admin.
- Backend ops:
  - Nuevo endpoint `POST /v1/auth/ops/accounts/:accountId/profiles` para crear perfil desde Admin.
  - El endpoint habilita entitlement de `p2` internamente y luego crea el segundo perfil.
- Backend hardening legacy:
  - Cuentas legacy en `trial` sin `expiresAt` ya no quedan en demo indefinido; se deriva expiracion desde `trialStartedAt/createdAt`.
  - Si una cuenta tiene `demoConsumedAt`, se normaliza fuera de `trial` para evitar inconsistencias.

Archivos tocados:
- `apps/web-app/public/auth/admin.html`
- `apps/web-app/public/auth/assets/v34-custom.css`
- `backend/src/server.js`
- `backend/src/authPersistence.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_023_admin_account_detail_profile2_creation_and_expiry_simplification.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Cuentas legacy con datos incompletos pueden cambiar a estado coherente en el primer ciclo de normalizacion y requerir validacion operativa.
- La limpieza de controles en Admin cambia el flujo operativo esperado por operadores acostumbrados al esquema anterior.

Pendiente de prueba:
- Crear `p2` desde Admin en cuenta con solo `p1`.
- Confirmar que al existir `p2` desaparece el bloque de creacion.
- Cambiar estado + expiracion (fecha/hora) con boton `Aplicar`.
- Verificar cuenta legacy que antes quedaba en demo indefinido y que ahora no permanezca en `trial` eternamente.

Resultado esperado:
- Admin opera detalle de cuenta con flujo mas directo y limpio.
- Segundo perfil se crea desde la propia seccion de perfiles sin checkbox separado.
- No queda comportamiento de demo infinito por datos legacy incompletos.

Pasos manuales externos:
- Ninguno en XUI.
- No requiere configuracion manual fuera del repo.


