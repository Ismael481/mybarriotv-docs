# TASK_050_qr_device_approve_base_url_runtime_fix

Estado: completed

Fecha:
2026-03-09

## Objetivo
Corregir redireccion del QR de login TV para que apunte a la IP LAN real del backend y sea accesible desde movil.

## Alcance
- Configuracion runtime local en `backend/.env`.
- Reinicio de backend.
- Sin cambios de logica de negocio.

## Archivos tocados
- `backend/.env` (local, ignorado por git)
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/02_tasks/TASK_050_qr_device_approve_base_url_runtime_fix.md`
- `docs/03_bugs/BUG_019_qr_url_localhost_not_reachable_from_mobile.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos
- Si la IP LAN cambia, el QR volvera a apuntar a direccion incorrecta hasta actualizar `AUTH_WEB_BASE_URL`.

## Pendiente de prueba
- Prueba funcional en movil escaneando QR real desde TV.

## Resultado esperado
- `POST /v1/auth/device/start` retorna `qrUrl` con `http://10.10.6.121:8080/...`.

## Pasos manuales si existen
1. Abrir flujo QR en TV.
2. Escanear QR desde movil conectado a la misma LAN.
3. Confirmar apertura de `/auth/device-approve` en `10.10.6.121:8080`.
