# ACTIVE_TASK

Tarea activa: **TASK_012_account_access_gate_foundation**

Estado actual:
- TASK_011: implementada.
- TASK_012: implementada en codigo (backend + TV + docs), pendiente validacion final en TV fisica.

Objetivo de cierre inmediato:
- Validar E2E final del gate de acceso en TV:
  - active + allowed entra Home
  - expired bloquea antes de Home
  - suspended bloquea antes de Home
  - blocked bloquea aunque login sea valido
  - QR respeta gate

Pendiente manual (usuario):
- Ejecutar validacion en TV fisica/LAN con cuentas en estados `active`, `expired`, `suspended`.
- Confirmar UX/mensajes de `AccessBlocked` por `reasonCode`.
