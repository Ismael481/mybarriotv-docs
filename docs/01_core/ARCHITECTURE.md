# ARCHITECTURE

Estructura general:
- apps/tv-app: aplicación del televisor
- apps/web-app: panel administrativo y portal cliente
- backend: API y lógica de negocio
- docs: documentación viva
- XUI: motor externo de contenido

Arquitectura objetivo:
App TV -> Backend propio -> XUI

Responsabilidades:
- XUI:
  - canales
  - VOD
  - acceso a contenido
- Backend:
  - autenticación
  - clientes
  - dispositivos
  - sesiones
  - reglas de negocio
  - promociones
  - integración con XUI
- Web App:
  - administración
  - portal cliente
- TV App:
  - consumo de contenido
  - sesión del usuario
  - perfiles
  - experiencia de reproducción

Regla de arquitectura:
La app no debe quedar acoplada directamente a XUI como arquitectura final.
