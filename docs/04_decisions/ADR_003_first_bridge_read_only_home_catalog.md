# ADR_003_first_bridge_read_only_home_catalog

Fecha: 2026-03-06
Estado: accepted

## Contexto
Se necesita iniciar la integracion real con XUI sin romper la arquitectura objetivo ni abrir alcance funcional amplio. La app TV aun depende de mocks y no existe bridge minimo validado end-to-end.

## Decision
El primer bridge oficial sera un flujo vertical minimo y read-only:
- Home catalog minimo (lista simple)
- Seleccion de item
- Resolucion de playback de prueba

Siempre bajo patron `App TV -> Backend propio -> XUI`, sin acceso directo de la app TV a XUI.

## Motivo
- Minimiza riesgo tecnico y de producto.
- Permite validar contrato backend y mapper XUI temprano.
- Facilita pruebas E2E con bajo costo de implementacion.
- Es reemplazable por integraciones mas completas sin refactor mayor.

## Consecuencias
- Se posponen auth, sesiones, device binding y reglas comerciales.
- El backend debe exponer contrato interno estable desde la primera iteracion.
- Se requiere preparar catalogo/stream de prueba en XUI para validar el flujo.

## Alternativas consideradas
- Integrar primero detalle completo de contenido: descartado por mayor alcance.
- Integrar directamente app TV -> XUI: descartado por romper arquitectura objetivo.
- Mantener solo mocks hasta backend completo: descartado por retrasar validacion real.
