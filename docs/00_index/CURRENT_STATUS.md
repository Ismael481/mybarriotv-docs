# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` sigue operativo y no se modifico su arquitectura.
- Se implemento base minima de autenticacion (TASK_006) para iniciar fase de sesion.

## Resultado de TASK_006
### Backend
- Modulo auth basico agregado.
- Endpoints nuevos:
  - `POST /v1/auth/login`
  - `GET /v1/auth/me` (JWT requerido)
  - `GET /v1/auth/protected` (JWT requerido, prueba)
- JWT HS256 generado/validado con secreto por env.
- Middleware auth aplicado a endpoints protegidos.
- Logs de auth y acceso protegido activos.

### TV app
- Login por usuario/password conectado al backend.
- Token guardado localmente en `UserSession` (DataStore existente).
- Header `Authorization: Bearer` agregado automaticamente a requests por interceptor.
- Guard de navegacion aplicado: sin sesion no entra a Home.

## Dependencias externas actuales
- XUI mantiene el rol de motor de contenido y no requiere cambios para TASK_006.
- Configuracion externa requerida: variables de entorno auth en backend.

## Riesgos actuales
- Seguridad minima: sin refresh token, sin roles, sin device binding (esperado por alcance).
- Si `AUTH_JWT_SECRET` no se personaliza en ambiente real, riesgo de seguridad.

## Proximo enfoque recomendado
- Validar TASK_006 en TV fisica y luego iniciar backend minimo para auth + dispositivo (fase siguiente planificada).
