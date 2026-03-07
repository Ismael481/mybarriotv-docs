# CHATGPT_CONTEXT

Fecha: 2026-03-07
Rama: `main`
Tarea activa: `TASK_012_account_access_gate_foundation`

## Resumen operativo
- TASK_012 implementa gate comercial minimo separado de autenticacion valida.
- Backend agrega `GET /v1/auth/access` y estados persistentes de cuenta/dispositivo.
- TV app consulta gate despues de login manual y QR exchange; si no hay acceso, no entra a Home.

## Cambios clave recientes
- Backend:
  - `accountStatus`: `trial|active|expired|suspended`.
  - `accessStatus` de dispositivo: `allowed|blocked` (compat con `active|revoked`).
  - endpoint `GET /v1/auth/access` + auditoria `auth_access_checked`.
- TV:
  - `BackendAuthApi.access()`.
  - `LoginViewModel` valida `canAccessApp` antes de `setLoggedIn`.
  - nuevo `AuthState.AccessBlocked` + pantalla `AccessBlocked`.

## Pruebas tecnicas ejecutadas
- Backend `node --check` en archivos modificados: OK.
- Casos minimos del gate validados con store temporal:
  - active+allowed => acceso true
  - expired => `ACCOUNT_EXPIRED`
  - suspended => `ACCOUNT_SUSPENDED`
  - blocked => `DEVICE_BLOCKED`
  - QR exchange respeta gate => `DEVICE_BLOCKED`
- Compilacion TV no ejecutable por entorno local (`jvm.cfg` faltante en JBR de Android Studio).

## Cambios manuales externos
- Ninguno para TASK_012.
- No hay cambios requeridos en XUI para este alcance.

## Leer en repo publico
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/02_tasks/TASK_012_account_access_gate_foundation.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`
