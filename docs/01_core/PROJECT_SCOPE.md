# PROJECT_SCOPE

## Nombre
MyBarrioTV-App

## Alcance de producto
Monorepo privado para centralizar:
- app TV (cliente principal),
- backend propio (logica de negocio y gate de acceso),
- web app minima (auth + operacion minima),
- documentacion viva del proyecto.

## Estado de fase actual (2026-03-07)
- Base monorepo consolidada.
- Bridge tecnico minimo con XUI implementado y operativo.
- Auth funcional end-to-end (manual, QR, OTP SMS, reset, binding).
- Gate de acceso comercial implementado y operable con gestion minima de estados.

## Prioridad funcional vigente
1. Estabilizar operacion del gate de acceso (cuenta/dispositivo).
2. Validar E2E en TV fisica/LAN.
3. Mantener trazabilidad y control documental.

## Restricciones vigentes
- Sin pagos ni billing en esta fase.
- Sin panel admin completo ni portal cliente completo.
- Sin migracion a DB en esta fase.
- Sin RBAC complejo en esta fase.
- Cambios pequenos, trazables y reversibles.
