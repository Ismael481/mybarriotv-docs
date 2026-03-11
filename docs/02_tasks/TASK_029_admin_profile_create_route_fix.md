# TASK_029_admin_profile_create_route_fix

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Corregir el flujo de Admin para que el boton `Agregar perfil` cree correctamente el segundo perfil de cuenta (`p2`).

Alcance:
- Backend routing ops:
  - ajustar matcher de ruta para `POST /v1/auth/ops/accounts/:accountId/profiles`.

Archivos tocados:
- `backend/src/server.js`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_029_admin_profile_create_route_fix.md`
- `docs/03_bugs/BUG_007_admin_profile_create_route_mismatch.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos:
- Ningun riesgo tecnico adicional; cambio puntual de enrutado.

Pendiente de prueba:
- En Admin: seleccionar cuenta sin `p2`, completar nombre/avatar y pulsar `Agregar perfil`.
- Confirmar refresco con `p2` creado en detalle/listado.

Resultado esperado:
- Creacion de `p2` funcional desde Admin sin errores de ruta.

Pasos manuales externos:
- Ninguno.


