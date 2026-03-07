# TASK_009_auth_persistence_and_device_binding_hardening

Fecha: 2026-03-07  
Estado: implementada y validada

## Objetivo
Hacer persistente y mas robusta la base de auth/device de TASK_008 sin romper login manual ni QR.

## Resultado
- Persistencia en archivo JSON para sesiones QR y dispositivos vinculados.
- Endpoint de revocacion basica de dispositivos.
- Rate limit basico en `start`, `approve`, `exchange`.
- Auditoria basica de eventos auth/device.
- Compatibilidad preservada con TV app.

## Persistencia aplicada
- Store: `AUTH_STORE_FILE` (default `backend/data/auth-store.json`).
- Estructuras:
  - `deviceLoginSessions`
  - `linkedDevicesByUser`
  - `auditEvents`

## Endpoints
Mantenidos:
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- `GET /v1/auth/devices`

Nuevo:
- `POST /v1/auth/devices/revoke`

## Reglas minimas de vinculacion
- Deduplicacion por `deviceKey` con prioridad:
  - `mac -> serial -> widevineId -> deviceId -> fingerprint`
- Estado por vinculo:
  - `active`
  - `revoked`
- Bloqueo:
  - si un device esta revocado para la cuenta, se bloquea login manual y exchange QR.

## Archivos tocados
- `backend/src/authPersistence.js`
- `backend/src/deviceLogin.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `docs/02_tasks/TASK_009_auth_persistence_and_device_binding_hardening.md`

## Riesgos actuales
- Store JSON es single-instance (no multi-instancia).

## Nota de continuidad
- La superficie web auth minima se formaliza en `apps/web-app` dentro de TASK_010.
