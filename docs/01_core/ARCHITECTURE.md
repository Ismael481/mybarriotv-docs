# ARCHITECTURE

## Estructura de monorepo
- `apps/tv-app/`: cliente TV Android existente.
- `apps/web-app/`: reservado para frontend web futuro.
- `backend/`: reservado para backend propio futuro.
- `docs/`: documentación viva y sincronizable.

## Arquitectura objetivo
`App TV -> Backend propio -> XUI`

## Principio de responsabilidades
- **XUI**: motor de contenido (catálogo y entrega de contenido).
- **Backend propio**: identidad, reglas de negocio, control de dispositivos, trazabilidad y adaptación hacia XUI.
- **TV App**: experiencia de usuario y reproducción.
- **Web App**: operación administrativa y portal cliente.

## Estado técnico real (hoy)
La TV app aún trabaja con flujos demo/mock y no está conectada a backend propio ni a XUI en producción.
