# ADR_002_xui_as_content_engine

- Estado: Aceptada
- Fecha: 2026-03-06

## Contexto
XUI será usado como fuente/motor de contenido, pero el proyecto necesita mantener control propio sobre identidad, reglas de negocio, dispositivos y evolución de producto.

## Decisión
Definir a XUI como **motor de contenido** y no como núcleo de la lógica principal de negocio.

Arquitectura objetivo: `App TV -> Backend propio -> XUI`.

## Consecuencias
### Positivas
- Menor acoplamiento directo de cliente TV con proveedor externo.
- Capacidad de cambiar/combinar proveedores en el futuro.
- Control centralizado de seguridad, sesiones y reglas de negocio.

### Riesgos
- Se agrega complejidad inicial al requerir backend propio como capa intermedia.
- Requiere definir contratos internos claros antes de la integración productiva.
