# BUG_020_env_loader_ignored_dotenv_when_env_key_present_empty

Estado: closed
Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Sintoma reportado
- Backend devolvia `Missing XUI_BASE_URL environment variable` aun con valor presente en `backend/.env`.

## Causa raiz
- Loader `.env` solo cargaba claves ausentes (`!(key in process.env)`).
- Si la variable existia en entorno con string vacio, bloqueaba el valor de `.env`.

## Correccion aplicada
- En `loadEnvFromFile` (`backend/src/server.js`) ahora se aplica valor de `.env` cuando:
  - la clave no existe, o
  - existe pero esta vacia.

## Archivos tocados
- `backend/src/server.js`
- `docs/03_bugs/BUG_020_env_loader_ignored_dotenv_when_env_key_present_empty.md`

## Validacion
- Backend toma `XUI_BASE_URL` desde `.env` local.
- `GET /v1/content/home` responde con catalogo real XUI.

## Paso manual externo requerido
- Ninguno.

