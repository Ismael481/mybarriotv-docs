# TASK_025_admin_can_activate_profile2_fix

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Corregir que desde Admin no se podia activar el segundo perfil (`p2`) en cuentas donde `profile2Enabled` estaba en `false`.

Alcance:
- Backend ops:
  - en `handleOpsProfileStatusUpdate`, cuando la accion es `p2 -> active`, se habilita `profile2Enabled=true` antes de aplicar el cambio de estado de perfil.
- No cambia UX ni contratos de payload en frontend.

Archivo tocado:
- `backend/src/server.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_025_admin_can_activate_profile2_fix.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Minimo: cambio acotado solo a activacion ops de `p2`.

Pendiente de prueba:
- Desde Admin, cambiar `p2` de `inactive` a `active` y confirmar persistencia.
- Verificar que `p1` sigue protegido (no desactivable).

Resultado esperado:
- Admin puede activar `p2` correctamente sin bloqueo por entitlement previo.

Pasos manuales externos:
- Ninguno.


