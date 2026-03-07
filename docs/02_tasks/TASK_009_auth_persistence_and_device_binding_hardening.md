# TASK_009_auth_persistence_and_device_binding_hardening

Fecha: 2026-03-07
Estado: implementada (pendiente validacion final en entorno del usuario)

## Objetivo
Hacer persistente y mas robusta la base de autenticacion/dispositivo de TASK_008, sin romper login manual ni login QR.

## Alcance implementado

### 1) Persistencia backend
- Se agrego persistencia en archivo JSON para:
  - sesiones de login por dispositivo (QR)
  - dispositivos vinculados por usuario
  - eventos basicos de auditoria
- Archivo por defecto: `backend/data/auth-store.json` (configurable por `AUTH_STORE_FILE`).

Modelo persistido minimo:
- `deviceLoginSessions`:
  - `id`, `status`, `createdAtMs`, `expiresAtMs`, `approvedBy`, `approvedAtMs`, `deniedAtMs`, `exchangedAtMs`, `createdByDevice`
- `linkedDevicesByUser`:
  - `deviceKey`, `status` (`active` / `revoked`), identidad de dispositivo, `firstLinkedAt`, `linkedAt`, `lastSeenAt`, `revokedAt`, `revokeReason`, `revokedBy`
- `auditEvents`:
  - eventos de start/approve/exchange/upsert/revoke

### 2) Endpoints mantenidos y ajustados
Se mantienen:
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- `GET /v1/auth/devices`

Se agrega:
- `POST /v1/auth/devices/revoke` (protegido JWT)

### 3) Reglas minimas de vinculacion
- Evitar duplicados simples por `deviceKey` (upsert por prioridad):
  - `mac -> serial -> widevineId -> deviceId -> fingerprint -> unknown-device`
- Estado de vinculo por dispositivo:
  - `active`
  - `revoked`
- Regla de bloqueo:
  - si un dispositivo esta `revoked` para la cuenta, se bloquea login manual y exchange QR para ese mismo `deviceKey`.

### 4) Endurecimiento inicial
- Expiracion de sesiones QR mantenida y limpiado de sesiones terminales por retencion configurable:
  - `AUTH_DEVICE_SESSION_TTL_SECONDS`
  - `AUTH_DEVICE_SESSION_RETENTION_SECONDS`
- Rate limiting basico anti abuso:
  - `POST /v1/auth/device/start`
  - `POST /v1/auth/device/approve`
  - `POST /v1/auth/device/exchange`
- Logs y auditoria basica:
  - eventos persistidos en `auditEvents`
  - logs backend para acciones auth/device

### 5) TV app
- Sin rediseño de UI.
- Compatibilidad preservada con login manual + QR.
- No se requirieron cambios funcionales nuevos para TASK_009 en pantalla.

## Archivos tocados
- `backend/src/authPersistence.js` (nuevo)
- `backend/src/deviceLogin.js`
- `backend/src/server.js`
- `backend/.env.example`
- `backend/README.md`
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `docs/02_tasks/TASK_009_auth_persistence_and_device_binding_hardening.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Como probar persistencia tras reinicio
1. Levantar backend con `AUTH_STORE_FILE` configurado.
2. Crear sesion QR: `POST /v1/auth/device/start`.
3. Hacer login manual o flujo QR y verificar `GET /v1/auth/devices`.
4. Reiniciar backend.
5. Repetir:
   - `GET /v1/auth/device/status/:sessionId` (debe seguir existiendo si no expiro)
   - `GET /v1/auth/devices` (debe mantener dispositivos vinculados).

## Validacion ejecutada en desarrollo
- `node --check` OK en `authPersistence.js`, `deviceLogin.js`, `server.js`.
- Smoke test local de flujo QR + devices en puerto `8094` OK.
- Test de persistencia tras reinicio en puerto `8095` OK:
  - session status persistido (`pending`)
  - devices persistidos (`count >= 1`)

## Riesgos actuales
- Persistencia local en archivo JSON (single-instance), no pensada para multi-instancia.
- Web de aprobacion sigue siendo minima servida por backend.

## Fuera de alcance (respetado)
- OTP SMS real
- registro web completo
- perfiles completos
- billing/suscripciones
- panel admin
- web app final
- refactor amplio de arquitectura

## Pendiente antes de OTP y perfiles
- Migrar persistencia a DB transaccional (cuando toque fase productiva).
- Definir politica completa de limites por cuenta y gestion de sesiones/dispositivos.
- Migrar web minima de aprobacion a `apps/web-app`.
