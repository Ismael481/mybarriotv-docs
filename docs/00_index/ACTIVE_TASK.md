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
- Confirmar bloqueo/habilitacion de OTP: input codigo bloqueado antes de SMS y `Registrar cuenta` deshabilitado hasta SMS + codigo.
- Confirmar fix de login por telefono tras registro (8 digitos / 53 / +53).
- Validar flujo completo de restablecimiento de contrasena por SMS OTP desde login web.
- Validar caso `PHONE_EXISTS` en registro mostrando enlace directo a `Restablecer contrasena`.
- Validar tambien `USERNAME_EXISTS` con enlace a reset y ausencia de autocompletado inicial invasivo.
- Validar que en mobile no aparezca scroll vertical inicial en login/registro tras compactacion responsive.
- Validar ajuste puntual de centrado y salto de linea en mobile para `Restablecer contrasena`.
- Validar copy final con `contraseña` (ñ) en UI de login/registro/restablecer.
- Validar que QR TV ya no abra web legacy y use login web nueva en modo `device-approve`.
- Validar limpieza de legado web (una sola superficie auth en `/auth/login` y rutas antiguas solo por redireccion).
- Validar redireccion post-login a perfil y auto-aprobacion TV en modo `device-approve`.
- Validar ausencia de `Missing bearer token` en `POST /v1/auth/device/approve` durante login QR web.
- Validar demo corto para cuentas registradas (`AUTH_ACCOUNT_DEMO_TTL_SECONDS`) y countdown coherente en perfil web.
- Validar auto-logout real en TV al expirar token (retorno automatico a pantalla login).
- Validar que en modo perfil web no aparezca el panel superior de `Registrarse`.
- Validar fix web QR/login: botones `Registrarse`/`Restablecer Contraseña` y submit login vuelven a funcionar (sin recarga muda de formulario).
- Validar UX TV post-expiracion demo: al auto-logout mostrar aviso claro de demo vencida + necesidad de suscripcion en pantalla login.
