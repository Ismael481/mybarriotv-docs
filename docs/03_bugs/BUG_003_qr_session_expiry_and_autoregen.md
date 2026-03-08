# BUG_003_qr_session_expiry_and_autoregen

Estado:
fixed

Fecha de deteccion:
2026-03-08

Ultima actualizacion:
2026-03-08

Descripcion:
El login QR podia quedar detenido con mensaje de expirado sin regenerar automaticamente en algunos errores; ademas, codigos QR viejos abiertos en navegador no devolvian UX clara de expiracion.

Sintomas:
- TV mostraba estado de QR vencido y no siempre regeneraba sola.
- En web, enlaces de QR viejos devolvian error ambiguo (`invalid/not found`) en lugar de instruir reescaneo.
- Sesiones `approved` no intercambiadas podian sobrevivir al TTL en ciertos escenarios.

Contexto:
Flujo `POST /v1/auth/device/start` + `GET /v1/auth/device/status/:sessionId` + `POST /v1/auth/device/approve` + `POST /v1/auth/device/exchange`.

Causa raiz:
- Polling de TV no trataba todos los fallos recuperables como trigger de regeneracion.
- Semantica de backend para sesion faltante/antigua no estaba unificada a expiracion QR.
- Limpieza de sesiones expiraba `pending`, pero no forzaba vencimiento por TTL de `approved` no intercambiadas.

Archivos revisados:
- `apps/tv-app/app/src/main/java/com/techlads/composetv/features/auth/LoginViewModel.kt`
- `apps/web-app/public/auth/device-approve.html`
- `apps/web-app/public/auth/login.html`
- `backend/src/authPersistence.js`
- `backend/src/deviceLogin.js`
- `backend/src/server.js`

Solucion aplicada:
- Auto-regeneracion en TV ante errores recoverables (`expired`, `not_found`, `already_exchanged`, `invalid_state`, `denied`).
- Limpieza de estado UI QR previo al regenerar.
- Backend responde `SESSION_EXPIRED` para status faltante/vencido y `EXPIRED` para approve/exchange de sesion vieja.
- Backend expira por TTL sesiones no intercambiadas (incluida `approved`).
- Backend expira sesiones `pending` previas de la misma TV al emitir nuevo QR.
- Web valida sesion QR al cargar y antes de aprobar; mensajes de expiracion/reescaneo claros.

Pendiente de validacion:
- Reproducir caso de QR viejo en navegador y confirmar mensaje `expirado, escanea nuevamente`.
- Confirmar que TV genera QR nuevo automaticamente tras expiracion sin accion manual.

Resultado esperado tras la correccion:
- Login QR robusto con rotacion automatica.
- Imposible reutilizar QR viejo para aprobar TV.
- UX consistente de expiracion en web y TV.
