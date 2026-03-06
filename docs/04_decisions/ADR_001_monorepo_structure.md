# ADR_001_monorepo_structure

- Estado: Aceptada
- Fecha: 2026-03-06

## Contexto
Se requiere consolidar app TV existente, backend futuro, web app futura y documentación en un único repositorio privado con trazabilidad y sincronización documental.

## Decisión
Adoptar estructura monorepo base:
- `apps/tv-app/`
- `apps/web-app/`
- `backend/`
- `docs/`

## Consecuencias
### Positivas
- Claridad de ownership por dominio.
- Mejor coordinación entre etapas de producto.
- Documentación versionada junto al código.

### Trade-offs
- Mayor disciplina requerida para evitar mezclar cambios de dominios distintos en un solo PR.
- Necesidad de reglas claras para evolución de tooling entre apps y backend.
