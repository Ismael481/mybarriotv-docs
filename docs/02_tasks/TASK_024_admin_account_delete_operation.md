# TASK_024_admin_account_delete_operation

Estado: implemented (pendiente validacion manual final)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Permitir eliminar cuentas cliente desde el panel Admin con flujo seguro y reflejo inmediato en listado/detalle.

Alcance:
- Backend:
  - nuevo helper de persistencia para borrar cuenta por `accountId`.
  - limpieza de indices (`accountIdByUsername`, `accountIdByPhone`) y `linkedDevicesByUser`.
  - nuevo handler ops para eliminacion con auditoria.
  - proteccion para evitar eliminar la cuenta de la sesion actual.
- API:
  - `DELETE /v1/auth/ops/accounts/:accountId`.
- Admin web:
  - nuevo boton `Eliminar cuenta` en `Detalle de cuenta`.
  - confirmacion antes de borrar.
  - refresco de listado y limpieza del panel detalle al eliminar.

Archivos tocados:
- `backend/src/authPersistence.js`
- `backend/src/server.js`
- `backend/README.md`
- `apps/web-app/public/auth/admin.html`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_024_admin_account_delete_operation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Eliminacion es irreversible en el store JSON actual.
- Se preserva historial de auditoria; solo se elimina registro de cuenta y dispositivos vinculados.

Pendiente de prueba:
- Eliminar cuenta cliente desde admin y verificar que desaparece del listado.
- Confirmar que no se puede eliminar la cuenta de operador con sesion activa (`ACCOUNT_DELETE_SELF_FORBIDDEN`).
- Confirmar que la UI vuelve a estado sin detalle tras eliminar.

Resultado esperado:
- Admin puede eliminar cuentas de forma controlada sin editar JSON manualmente.

Pasos manuales externos:
- Ninguno en XUI.
- Sin cambios manuales fuera del repo.
