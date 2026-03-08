# BUG_002_profile_edit_method_not_allowed

Estado:
fixed

Fecha de deteccion:
2026-03-08

Ultima actualizacion:
2026-03-08

Descripcion:
Al editar nombre/avatar de un perfil en `/auth/login?mode=profile`, el guardado mostraba `Method Not Allowed` y no aplicaba el cambio.

Sintomas:
- Modal `Editar perfil` devuelve error al pulsar `Guardar perfil`.
- No se persiste el cambio de avatar o nombre.

Contexto:
Ocurre en integraciones donde el endpoint de update de perfil responde 405 al metodo enviado por frontend.

Causa probable:
Desalineacion de metodo HTTP entre entornos (frontend/backend) para ` /v1/auth/profiles/:profileId`.

Archivos revisados:
- `apps/web-app/public/auth/login.html`
- `backend/src/server.js`

Solucion aplicada:
- Frontend: fallback de guardado `POST` y si recibe 405 intenta `PUT` sobre el mismo endpoint.
- Backend: se aceptan `POST|PUT|PATCH` en ` /v1/auth/profiles/:profileId`.

Pendiente de validacion:
- Validar manualmente en entorno del usuario que `Guardar perfil` no retorna 405.
- Confirmar que avatar y nombre se reflejan al recargar perfil.

Resultado esperado tras la correccion:
- El modal guarda correctamente nombre/avatar del perfil.
- No aparece `Method Not Allowed` en el flujo normal de edicion.
