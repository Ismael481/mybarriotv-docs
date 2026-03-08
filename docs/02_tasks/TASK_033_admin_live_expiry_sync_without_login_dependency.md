# TASK_033_admin_live_expiry_sync_without_login_dependency

Estado: implemented (pendiente validacion manual)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Hacer que el panel Admin refleje expiracion real de cuentas aunque el cliente no vuelva a iniciar sesion, y que se actualice en pantalla sin recargar manualmente.

Alcance:
- Backend persistencia/auth:
  - reconciliar expiraciones por `expiresAt` en operaciones de lectura de cuentas/perfiles.
  - transicionar automaticamente a `expired` con auditoria correspondiente.
- Web admin:
  - auto-refresh cada 15s de listado y detalle de cuenta cuando la pestaña esta visible.

Archivos tocados:
- `backend/src/authPersistence.js`
- `apps/web-app/public/auth/admin.html`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_033_admin_live_expiry_sync_without_login_dependency.md`
- `docs/03_bugs/BUG_011_admin_expiry_state_stale_until_customer_login.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Refresco automatico puede mostrar cambios con retardo maximo del intervalo configurado (~15s).

Pendiente de prueba:
- Configurar `expiresAt` cercano desde admin.
- Esperar vencimiento sin login del cliente.
- Verificar que admin cambia a `expired` automaticamente (sin recarga manual).

Resultado esperado:
- Estado de cuentas en admin consistente con expiracion real de backend en tiempo casi real.

Pasos manuales externos:
- Ninguno.
