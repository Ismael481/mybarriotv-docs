# TASK_007_auth_experience_and_device_login_design

Fecha: 2026-03-07
Estado: activa (diseno/documentacion)

## Objetivo
Definir el diseno final de experiencia y arquitectura de autenticacion para MyBarrioTV, reutilizando la base de TASK_006 y evitando retrabajo en fases posteriores.

## Alcance de esta fase
- Disenar y documentar un unico sistema auth con dos canales de entrada en TV:
  - login manual (usuario/telefono + password)
  - login por QR (aprobacion desde web/movil)
- Disenar flujo web de inicio/registro preparado para OTP SMS futuro.
- Definir separacion de modelo entre Cuenta y Perfiles.
- Preservar la UI actual de la app TV (tipografia/layout/estetica).

## Fuera de alcance (respetado)
- Implementacion real de QR end-to-end.
- Implementacion real de OTP SMS.
- Device binding completo.
- Billing/suscripciones/planes/roles admin.
- Redisenar pantallas actuales.

## Principio de arquitectura
Debe existir una sola arquitectura auth, con distintos canales de entrada, no dos sistemas separados.

- `Auth Core` (backend JWT ya existente) sigue siendo el emisor/validador de sesion.
- `Canal TV Manual` y `Canal TV QR` convergen al mismo resultado: token de sesion para la TV.
- `Web Auth` actua como canal de aprobacion/autenticacion humana, no como bypass directo del backend.

## Flujo A: TV login manual
1. Pantalla Login TV muestra simultaneamente:
   - formulario manual (usuario/telefono + password)
   - bloque QR con CTA de escaneo.
2. Usuario ingresa credenciales en TV.
3. TV llama `POST /v1/auth/login`.
4. Backend retorna JWT.
5. TV guarda sesion local y desbloquea Home.

Notas de UI:
- Mantener layout y componentes actuales.
- Solo extender contenido para convivir con opcion QR.

## Flujo B: TV login por QR (diseno)
1. TV solicita un `loginSession` temporal al backend (pendiente implementacion de endpoint dedicado).
2. TV renderiza QR con URL web propia, ejemplo conceptual:
   - `https://auth.mybarriotv.com/tv-login?session=...`
3. Usuario escanea desde movil.
4. Web autentica al usuario (login o registro).
5. Web muestra pantalla de aprobacion: "Autorizar este TV".
6. Al aprobar, backend marca `loginSession` como autorizada.
7. TV consulta estado (polling corto o SSE/websocket en fase futura).
8. Cuando estado sea `approved`, backend entrega token para TV.
9. TV persiste sesion y entra a Home automaticamente.

Regla clave:
- El QR nunca autentica directo en TV. Siempre pasa por web propia + backend.

## Flujo C: Web auth + autorizacion de TV (diseno)
Web minima requerida:
- Login web.
- Registro web.
- Pantalla de aprobacion de sesion TV.

Secuencia:
1. Abrir URL del QR.
2. Si no hay sesion web, mostrar login/registro.
3. Una vez autenticado, mostrar detalle de sesion TV (codigo corto, hora, dispositivo).
4. Usuario confirma autorizacion.
5. Backend confirma a TV por estado de `loginSession`.

## Flujo D: Registro web preparado para OTP SMS (diseno futuro)
En esta fase no se integra proveedor real OTP.

Diseno requerido:
- Paso de captura de telefono en registro.
- Estado de cuenta con flags para verificacion:
  - `phoneVerificationStatus`: `pending|verified|failed`
- Punto de extension backend para OTP:
  - `sendOtp(phone)`
  - `verifyOtp(code)`

Politica de avance:
- No habilitar OTP real hasta cerrar contrato de API, proveedor y reglas anti-fraude.

## Modelo de identidad: Cuenta vs Perfil
### Cuenta (identidad principal)
- Owner legal y credenciales.
- Datos de acceso (email/telefono/password/estado verificacion).
- Gestion de sesiones/dispositivos.

### Perfil (subidentidad de consumo)
- Nombre/avatar/preferencias/control parental.
- Historial y personalizacion por perfil.
- Siempre dependiente de una Cuenta.

En TASK_007:
- Solo definido el modelo y la separacion semantica.
- No implementar aun selector completo de perfiles.

## Riesgos
- Si QR flow se implementa sin `loginSession` temporal, se puede romper trazabilidad/seguridad.
- Si web/TV divergen contratos, aparecen dos auth paralelos.
- Si OTP se integra sin diseno previo de estados, se fuerza migracion de usuarios.
- Si se redisenan pantallas en esta fase, se pierde estabilidad visual ya validada.

## Criterio de exito de TASK_007
- Queda documentado un diseno unico de auth con canales manual y QR.
- Queda definido el flujo web de autorizacion de sesion TV.
- Queda definido como se enchufa OTP futuro sin retrabajar TASK_006.
- Queda documentada la separacion Cuenta/Perfil sin implementar perfiles aun.
- Se preserva explicitamente la UI actual de TV app.

## Orden recomendado de implementacion (fases siguientes)
1. Backend: contrato `loginSession` para QR (create/status/approve/exchange).
2. TV app: pantalla login dual (manual + QR simultaneo) conservando UI base.
3. Web app: login/registro + pantalla de aprobacion de sesion TV.
4. TV app: auto-login al aprobar sesion web.
5. Modelo de perfiles sobre cuenta (solo base de datos/API minima).
6. OTP SMS real en web registro (cuando contrato y proveedor esten cerrados).

## Dependencias externas / cambios manuales fuera del repo
- XUI: sin cambios en TASK_007.
- Para OTP futuro: proveedor SMS externo pendiente de seleccionar/configurar (no implementar en esta fase).

## Archivos tocados en esta tarea
- `docs/02_tasks/TASK_007_auth_experience_and_device_login_design.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
