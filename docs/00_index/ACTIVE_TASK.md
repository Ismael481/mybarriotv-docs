# ACTIVE_TASK

Tarea activa: **TASK_018_customer_account_completion_and_self_service_hardening**

Estado actual:
- TASK_013: validada.
- TASK_014: implementada y validada (backend + web + docs).
- TASK_015: validada.
- TASK_016: implementada en codigo y docs; pendiente validacion visual/manual final.
- TASK_017: implementada en codigo y docs; pendiente validacion visual/manual final.
- TASK_018: implementada en codigo y docs; pendiente validacion visual/manual final.

Objetivo de cierre inmediato:
- Validar perfil cliente/admin en capa operativa minima:
  - completion obligatorio solo de correo al primer acceso en profile.
  - edicion de cuenta en modal (blur) para username/correo/telefono/contrasena.
  - telefono y contrasena cambian con OTP dentro del modal.
  - perfiles con `active|inactive` visibles en cliente.
  - cada perfil tiene lapiz para editar nombre y avatar.
  - operador puede activar/desactivar perfiles desde `/admin`.
  - al expirar/suspender cuenta, perfiles quedan inactivos por consistencia comercial.
