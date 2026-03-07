# CHATGPT_CONTEXT

Fecha:
2026-03-07

Rama actual:
main

Tarea activa:
TASK_007_auth_experience_and_device_login_design (diseno/documentacion)

Resumen corto:
`TASK_006_authentication_foundation` ya quedo implementada (backend JWT + login/me/protected + sesion local TV + guard Home).
La fase actual (`TASK_007`) define el diseno final de experiencia auth para evitar retrabajo: login manual TV + login QR TV, aprobacion desde web/movil, registro web preparado para OTP y separacion Cuenta/Perfil.

Correccion de consistencia documental:
- El estado oficial del repo privado deja `TASK_006` como implementada.
- `TASK_007` queda marcada como nueva tarea activa.
- Estos cambios quedan listos para sincronizacion al mirror publico desde `docs/`.

Archivos modificados en TASK_007:
- docs/02_tasks/TASK_007_auth_experience_and_device_login_design.md
- docs/00_index/ACTIVE_TASK.md
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/CHATGPT_CONTEXT.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Archivos recomendados para revision por ChatGPT (repositorio publico):
- docs/00_index/CURRENT_STATUS.md
- docs/00_index/ACTIVE_TASK.md
- docs/02_tasks/TASK_006_authentication_foundation.md
- docs/02_tasks/TASK_007_auth_experience_and_device_login_design.md
- docs/05_changelog/CHANGELOG_2026_Q1.md

Pendiente de prueba por el usuario:
- No hay pruebas funcionales nuevas en TASK_007 (fase documental).
- Se mantiene pendiente validacion en TV fisica de TASK_006.

Riesgos o bloqueos:
- OTP SMS depende de proveedor externo aun no definido.
- QR login requiere contratos backend/web/TV coordinados para evitar doble sistema auth.

Cambios manuales externos requeridos:
- XUI: ninguno para TASK_007.
- OTP futuro: seleccionar y configurar proveedor SMS fuera del repo (pendiente).

Siguiente fase recomendada:
- Implementar backend `loginSession` para QR segun diseno de TASK_007, antes de integrar web y aprobacion de sesion TV.
