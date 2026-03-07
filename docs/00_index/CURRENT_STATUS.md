# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` operativo y estable.
- `TASK_006_authentication_foundation` ya implementada en backend y TV app.
- Nueva fase activa: `TASK_007_auth_experience_and_device_login_design` (solo diseno/documentacion).

## Estado real de auth (implementado)
### Backend
- `POST /v1/auth/login`
- `GET /v1/auth/me`
- `GET /v1/auth/protected`
- JWT base y middleware auth funcionando.

### TV app
- Login manual minimo conectado a backend.
- Sesion local persistida.
- Guard de navegacion activo para bloquear Home sin sesion.

## Diseno objetivo abierto en TASK_007
- Pantalla TV con login manual y login QR visibles simultaneamente.
- Flujo QR via web propia con aprobacion de sesion TV.
- Registro web preparado para OTP SMS futuro.
- Separacion de modelo: Cuenta (principal) vs Perfil (subidentidad de consumo).

## Dependencias externas actuales
- XUI no requiere cambios para TASK_007.
- OTP futuro depende de proveedor SMS externo aun no seleccionado.

## Riesgos actuales
- Implementar QR u OTP sin cerrar contratos puede forzar retrabajo de TASK_006.
- Desalinear web/TV en auth puede crear dos sistemas de login paralelos.

## Proximo enfoque recomendado
- Tomar TASK_007 como blueprint obligatorio y luego implementar por fases: backend loginSession -> TV QR -> web aprobacion -> OTP real.
