# CURRENT_STATUS

## Estado general actual (2026-03-08)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Flujo auth y gate de acceso: operativo (manual + QR + OTP + control de dispositivos + ops minima).
- TASK_006 a TASK_036: implementadas segun historial documental.
- TASK_036 validada manualmente: al expirar/suspender cuenta en caliente, TV pasa a `AccessBlocked` de inmediato.
- Se ejecuto auditoria integral y se consolidaron riesgos de cierre pendientes.

## Debilidades principales detectadas
- Critico (seguridad/config): `backend/.env` esta versionado y contiene variables sensibles; ademas existe fallback inseguro de `AUTH_JWT_SECRET`.
- Alto (portabilidad): TV app fija backend por IP LAN en build (`BACKEND_BASE_URL`) y usa `org.gradle.java.home` local no portable.
- Alto (mantenibilidad): backend concentrado en `backend/src/server.js` (~2581 lineas) con muchas rutas y reglas en un solo archivo.
- Alto (mantenibilidad frontend): `apps/web-app/public/auth/login.html` y `admin.html` concentran logica JS inline extensa sin modularizacion.
- Alto (calidad): no hay suite de pruebas automatizadas activa para backend/TV/web en el estado actual del repo.

## Incongruencias operativas
- El flujo ops usa `DELETE` (`/v1/auth/ops/accounts/:accountId`), pero CORS declara solo `GET, POST, PUT, PATCH, OPTIONS`; en origen cruzado ese delete puede fallar por preflight.
- La documentacion core conserva algunos archivos plantilla con codificacion mojibake, lo cual baja claridad editorial.

## Validaciones tecnicas en esta auditoria
- `node --check` backend: OK.
- Build TV local: bloqueado por JBR local (`jvm.cfg`), sin validacion de compilacion Android en este entorno.

## Recomendacion de siguiente fase (orden)
1. Hardening de seguridad y configuracion (secrets/env/build portability).
2. Base minima de pruebas automatizadas (backend + smoke TV/web).
3. Modularizacion incremental de backend auth/ops sin cambio de arquitectura.
4. Modularizacion de JS web auth/admin para reducir regresiones.
