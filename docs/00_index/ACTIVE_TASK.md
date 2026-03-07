# ACTIVE_TASK

Tarea activa: **TASK_011_sms_otp_registration_and_verification**

Estado actual:
- TASK_009: implementada y validada.
- TASK_010: implementada (surface web auth en `apps/web-app`).
- TASK_011: implementada en codigo, en etapa de cierre por validacion E2E final.

Objetivo de cierre inmediato:
- Confirmar E2E final: registro SMS -> login web -> aprobacion QR TV -> acceso TV.
- Confirmar expiracion demo: auto-logout TV + aviso de demo expirada.
- Confirmar UX login TV nueva: QR izquierda / login derecha, sin boton de regenerar (autogeneracion).
- Dejar listo el traspaso a la siguiente tarea (OTP productivo/perfiles).

Pendiente manual (usuario):
- Validacion final en TV fisica y movil en red LAN.
- Confirmacion funcional final del flujo completo con cuenta real.
