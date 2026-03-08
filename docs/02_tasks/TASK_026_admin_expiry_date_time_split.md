# TASK_026_admin_expiry_date_time_split

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Separar la expiracion de cuenta en Admin en campos independientes de fecha y hora, con boton dedicado `Guardar`, desacoplado del boton `Aplicar` de estado de cuenta.

Alcance:
- Admin UI:
  - reemplazo de `datetime-local` por `date` + `time`.
  - nuevo boton `Guardar` para expiracion.
  - `Aplicar` ahora actualiza solo `accountStatus`.
- Backend ops:
  - nuevo endpoint `POST /v1/auth/ops/accounts/:accountId/expiry`.
  - actualiza solo `expiresAt` sin mutar estado de cuenta.

Archivos tocados:
- `apps/web-app/public/auth/admin.html`
- `backend/src/server.js`
- `backend/README.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_026_admin_expiry_date_time_split.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Cambio de flujo operativo en Admin (estado y expiracion ahora son acciones separadas).

Pendiente de prueba:
- Seleccionar fecha + hora y guardar expiracion desde Admin.
- Cambiar estado con `Aplicar` y confirmar que no altera expiracion.

Resultado esperado:
- Expiracion gestionada por fecha/hora separadas y guardado dedicado.

Pasos manuales externos:
- Ninguno.
