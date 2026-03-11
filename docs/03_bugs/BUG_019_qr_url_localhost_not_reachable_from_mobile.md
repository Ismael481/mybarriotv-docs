# BUG_019_qr_url_localhost_not_reachable_from_mobile

Estado: closed
Fecha de deteccion:
2026-03-09

Ultima actualizacion:
2026-03-09

## Sintoma reportado
- QR de login TV no redireccionaba en movil.
- `qrUrl` retornaba `http://localhost:8080/...`.

## Causa raiz
- `AUTH_WEB_BASE_URL` en runtime local estaba configurado a `http://localhost:8080`.

## Correccion aplicada
- `backend/.env` actualizado:
  - `AUTH_WEB_BASE_URL=http://10.10.6.121:8080`
- Reinicio backend para aplicar configuracion.

## Archivos tocados
- `backend/.env` (local, ignorado por git)
- `docs/03_bugs/BUG_019_qr_url_localhost_not_reachable_from_mobile.md`

## Validacion
- `POST /v1/auth/device/start` devuelve:
  - `qrUrl: http://10.10.6.121:8080/auth/device-approve?...`

## Paso manual externo requerido
- Confirmar que movil y TV estan en la misma red LAN y que puerto `8080` esta accesible desde el movil.

