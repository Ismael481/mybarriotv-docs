# CURRENT_STATUS

## Estado general
- Bridge `App TV -> Backend -> XUI` operativo.
- `TASK_006_authentication_foundation` cerrada e implementada.
- `TASK_007_auth_experience_and_device_login_design` cerrada como diseno.
- `TASK_008_qr_device_login_implementation` cerrada como implementada.
- `TASK_009_auth_persistence_and_device_binding_hardening` implementada y en validacion final.

## Estado auth/dispositivos actual
### Core auth
- `POST /v1/auth/login`
- `GET /v1/auth/me`
- `GET /v1/auth/protected`

### Device login QR
- `POST /v1/auth/device/start`
- `GET /v1/auth/device/status/:sessionId`
- `POST /v1/auth/device/approve`
- `POST /v1/auth/device/exchange`
- `GET /auth/device?sessionId=...`

### Device binding
- `GET /v1/auth/devices`
- `POST /v1/auth/devices/revoke`
- Estados de vinculo: `active`, `revoked`
- Deduplicacion por `deviceKey` (prioridad: mac -> serial -> widevineId -> deviceId -> fingerprint)

## Persistencia
- Sesiones QR persistidas en archivo JSON (`AUTH_STORE_FILE`).
- Dispositivos vinculados persistidos en el mismo store.
- Estado sobrevive reinicio del backend (validado en entorno local de desarrollo).

## Endurecimiento aplicado
- Expiracion y limpieza de sesiones QR con retencion configurable.
- Rate limit basico anti abuso en `start`, `approve`, `exchange`.
- Auditoria basica en store (`auditEvents`) + logs backend.

## Riesgos actuales
- Persistencia en archivo JSON (single-instance), no equivalente a DB productiva.
- Web de aprobacion sigue como implementacion minima en backend.

## Proximo enfoque recomendado
- Validacion final en entorno del usuario (TV + movil + reinicio backend real).
- En siguiente fase, migrar store a DB y mover web de aprobacion a `apps/web-app`.
