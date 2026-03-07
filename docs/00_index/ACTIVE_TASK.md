# ACTIVE_TASK

Tarea activa: **TASK_011_sms_otp_registration_and_verification**

Estado:
- `TASK_009` implementada y validada.
- `TASK_010` implementada (web auth surface en `apps/web-app`).
- `TASK_011` implementada en codigo, pendiente validacion final del usuario con proveedor SMS real.

Objetivo activo de cierre:
- Validar registro OTP SMS real (request/verify/complete) en entorno del usuario y confirmar login posterior (manual + QR).
- Validar ajustes finales UX web (alineacion formulario y errores amigables de codigo SMS).
- Confirmar micro-ajuste visual final solicitado en registro (fila codigo SMS + boton enviar + icono mostrar contrasena).
- Confirmar ajuste final: fila SMS alineada exacta con inputs y ojo de password visible sin fondo circular.
- Confirmar ajuste visual puntual: ojo claramente visible, boton `Registrar cuenta` ancho input y texto telefono corregido.
- Confirmar ajuste final: placeholder `Telefono +53XXXXXXXX` y ojito visible en todos los navegadores.
- Confirmar warning de password en blur y placeholder `Contrasena` en registro.
- Confirmar warning en blur para telefono con formato invalido.
