# TASK_037_comprehensive_project_audit_and_next_steps

Estado: completed

Fecha de creacion:
2026-03-08

Ultima actualizacion:
2026-03-08

Objetivo:
Realizar auditoria tecnica integral del proyecto para identificar debilidades, incongruencias y definir el siguiente orden de trabajo.

Motivo:
Se requiere un diagnostico claro y actualizado para decidir la proxima fase sin mezclar refactor amplio con nuevas funcionalidades.

Alcance:
- Revisar estado real de `apps/tv-app`, `apps/web-app`, `backend` y `docs`.
- Verificar consistencia entre codigo y documentacion indice.
- Identificar riesgos tecnicos abiertos y modulos no cerrados.
- Dejar resumen accionable para priorizacion siguiente.

No tocar:
- No cambiar arquitectura global.
- No introducir features nuevas.
- No modificar logica funcional fuera de ajustes documentales.

Archivos tocados:
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/02_tasks/TASK_037_comprehensive_project_audit_and_next_steps.md`
- `docs/01_core/PROMPT_RULES.md`
- `docs/02_tasks/TASK_XXX_nombre.md`
- `docs/03_bugs/BUG_XXX_nombre.md`
- `docs/04_decisions/ADR_XXX_nombre.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

Riesgos detectados:
- Critico: secretos y credenciales en `backend/.env` versionado.
- Alto: fallback inseguro `AUTH_JWT_SECRET` si no existe valor productivo.
- Alto: backend monolitico en `backend/src/server.js` (~2581 lineas) con alta superficie de regresion.
- Alto: frontend web auth/admin con JS inline extenso (`login.html` y `admin.html`), bajo aislamiento por modulo.
- Alto: ausencia de pruebas automatizadas y pipeline de validacion tecnica.
- Medio: build Android bloqueado por configuracion local fija (`org.gradle.java.home` en `apps/tv-app/gradle.properties`).
- Medio: posible fallo CORS en `DELETE` desde origen cruzado por metodos declarados incompletos.

Pendiente de prueba:
- Validar build Android en entorno con JBR correcto.
- Ejecutar smoke E2E real: login manual/QR, gate runtime por expiracion y flujo ops admin.
- Probar endpoints ops en origen cruzado para confirmar comportamiento CORS real.

Resultado esperado:
- Base documental clara para decidir la siguiente tarea tecnica sin ambiguedades.
- Prioridad recomendada:
  1) hardening seguridad/configuracion,
  2) pruebas automatizadas minimas,
  3) modularizacion backend,
  4) modularizacion web auth/admin.

Pasos manuales externos:
- Rotar credenciales actuales si `backend/.env` contiene valores reales.
- Ajustar gestion de secretos fuera del repo (variables de entorno por entorno, sin versionar `.env` real).
