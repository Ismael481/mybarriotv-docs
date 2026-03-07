# ARCHITECTURE

## Estructura de monorepo
- `apps/tv-app/`: cliente TV Android (principal).
- `apps/web-app/`: superficie web minima de auth/perfil/operacion.
- `backend/`: backend propio (auth, reglas de acceso, bridge XUI).
- `docs/`: documentacion viva sincronizable.

## Arquitectura objetivo
`App TV -> Backend propio -> XUI`

## Responsabilidades
- **XUI**: catalogo y entrega de contenido.
- **Backend propio**: identidad, sesiones, gate comercial, control de dispositivos, auditoria y adaptacion a XUI.
- **TV app**: experiencia de usuario, reproduccion y consumo del gate de acceso.
- **Web app**: auth web minima y operacion minima de estados de acceso (no panel admin completo).

## Estado tecnico real (2026-03-07)
- Bridge TV->Backend->XUI operativo en entorno actual.
- Auth implementada con:
  - login manual
  - login QR
  - registro OTP SMS
  - reset password OTP SMS
  - device binding persistente
- Gate comercial activo por cuenta/dispositivo (`/v1/auth/access`).
- Operacion minima de acceso disponible via backend ops + web profile.
- Persistencia actual en JSON store (sin DB productiva aun).

## Limites vigentes
- Sin billing/pagos.
- Sin RBAC completo.
- Sin panel admin/portal cliente completos.
- Sin migracion a DB en esta fase.
