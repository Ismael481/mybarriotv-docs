# CURRENT_STATUS

## Estado general actual (2026-03-08)
- Bridge `App TV -> Backend -> XUI`: operativo.
- Flujo auth y gate de acceso: operativo (manual + QR + OTP + control de dispositivos + ops minima).
- TASK_006 a TASK_036: implementadas segun historial documental.
- TASK_036 validada manualmente: al expirar/suspender cuenta en caliente, TV pasa a `AccessBlocked` de inmediato.
- TASK_038 hardening tecnico base: completada.

## Riesgos cerrados en TASK_038
- `backend/.env` eliminado del repo y bloqueado por `.gitignore`.
- `AUTH_JWT_SECRET` sin fallback inseguro; backend falla al boot si falta secreto fuerte.
- CORS actualizado para metodos ops reales, incluyendo `DELETE` en preflight.
- `org.gradle.java.home` removido de `apps/tv-app/gradle.properties` (sin path local fijo en repo).

## Riesgos abiertos actuales
- Mantenibilidad: backend sigue monolitico en `backend/src/server.js` (~2581 lineas).
- Mantenibilidad frontend: `login.html` y `admin.html` mantienen JS inline extenso.
- Calidad: no hay suite automatizada robusta para backend/TV/web.
- Entorno local: en esta maquina `JAVA_HOME` global apunta a JBR invalido y bloquea Gradle, aunque el repo ya no fija esa ruta.

## Validaciones tecnicas TASK_038
- `node --check` en backend (`auth.js`, `server.js`): OK.
- Auth config:
  - sin `AUTH_JWT_SECRET` fuerte: backend falla de forma explicita.
  - con `AUTH_JWT_SECRET` fuerte: backend arranca correctamente.
- Preflight CORS `DELETE` a ruta ops: `204` con `Access-Control-Allow-Methods` incluyendo `DELETE`.
- Build TV: referencia local fija removida del repo; entorno actual sigue fallando por `JAVA_HOME` externo.

## Recomendacion de siguiente fase
1. Base minima de pruebas automatizadas (backend + smoke TV/web).
2. Modularizacion incremental de backend auth/ops sin cambiar arquitectura de producto.
3. Modularizacion de JS web auth/admin para reducir riesgo de regresion.
