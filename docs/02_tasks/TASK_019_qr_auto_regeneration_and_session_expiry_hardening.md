# TASK_019_qr_auto_regeneration_and_session_expiry_hardening

Estado: completed (cierre administrativo; pruebas manuales listadas en TASK_053)

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Corregir la regeneracion automatica de QR en TV y endurecer seguridad/UX de expiracion para que codigos viejos no se puedan reutilizar desde web.

Alcance:
- TV app:
  - robustecer polling QR para regenerar automaticamente en errores recuperables.
  - evitar que quede visible un QR viejo mientras se regenera.
- Backend:
  - garantizar expiracion por TTL tambien para sesiones `approved` no intercambiadas.
  - invalidar sesiones `pending` previas de la misma TV al crear un nuevo QR.
  - tratar sesiones ausentes/viejas como expiradas/invalidas en status/approve/exchange.
- Web:
  - validar `sessionId` QR antes de permitir login/aprobacion.
  - mostrar mensaje claro de QR expirado/reescaneo para session vieja.

No tocar:
- Billing/pagos.
- Arquitectura global.
- Integracion XUI.

Archivos involucrados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/web-app/public/auth/device-approve.html`
- `apps/web-app/public/auth/login.html`
- `backend/src/authPersistence.js`
- `backend/src/deviceLogin.js`
- `backend/src/server.js`
- `backend/README.md`
- `docs/03_bugs/BUG_003_qr_session_expiry_and_autoregen.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Implementacion realizada:
- TV `LoginViewModel`:
  - se agrega scheduler de regeneracion (`qrRegenerationJob`) para errores QR recuperables.
  - `startDeviceLogin()` limpia `sessionId`, `qrUrl` y `expiresAt` para no dejar QR viejo en pantalla.
  - si polling devuelve `expired/denied`, regenera automaticamente.
  - si polling/start falla con codigos recuperables (`expired`, `not_found`, `already_exchanged`, `invalid_state`, `denied`), se regenera automaticamente.
- Backend:
  - `cleanupExpiredDeviceLoginSessions` ahora expira cualquier sesion no intercambiada al vencer TTL (incluye `approved`).
  - nuevo helper `expirePendingDeviceLoginSessionsByDevice` para expirar QRs pendientes anteriores de la misma TV al generar uno nuevo.
  - `approveDeviceLoginSession` y `exchangeApprovedDeviceLoginSession` devuelven `EXPIRED` (410) cuando la sesion falta o es vieja.
  - `GET /v1/auth/device/status/:sessionId` devuelve `410 SESSION_EXPIRED` cuando no existe/vencio.
- Web:
  - `device-approve.html` valida sesion QR al cargar y bloquea formulario si ya no es `pending`.
  - `device-approve.html` revalida sesion antes de credenciales/aprobacion.
  - `login.html` (modo `device-approve` legacy) verifica sesion `pending` antes de login.
  - mensajes UX para `EXPIRED/SESSION_EXPIRED/ALREADY_EXCHANGED/INVALID_STATE` orientan a re-escanear QR.

Riesgos:
- No se ejecuto build completa de Android en este ciclo (se validaron solo archivos JS/Node).
- Si hay clientes muy antiguos con rutas QR legacy, dependeran del flujo de validacion incorporado en `login.html`.

Pendiente de prueba:
- Dejar QR en TV hasta expirar y verificar autogeneracion sin accion manual.
- Abrir enlace QR viejo en navegador y comprobar mensaje de expirado + no aprobacion.
- Aprobar con QR vigente y confirmar login QR normal.
- Flujo registrar -> login -> aprobar TV con QR vigente.

Resultado esperado:
- Nunca queda bloqueado en “QR expirado, regenera” sin autorotacion.
- Un QR viejo no se puede usar para aprobar TV.
- La web informa claramente cuando el QR esta vencido y pide escaneo nuevo.

Pasos manuales si existen:
- Reiniciar backend para activar cambios de expiracion/estado QR.
- No hay cambios manuales en XUI para esta tarea.


