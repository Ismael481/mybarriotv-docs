# TASK_069_auth_device_db_transition_closeout

Estado: completed

Fecha de creacion:
2026-03-12

Ultima actualizacion:
2026-03-12

## Objetivo
Cerrar de forma controlada la transicion operativa de auth/device/session hacia DB y dejar reglas claras de operacion, respaldo y recuperacion.

## Alcance
- Auditoria de pendientes restantes de la transicion auth/device/session.
- Cierre de brechas pequenas documentales/operativas.
- Definicion explicita de:
  - fuente de verdad definitiva;
  - estrategia de respaldo;
  - variables de entorno y rutas persistentes;
  - limites conocidos;
  - procedimiento minimo de recuperacion.
- Alineacion final de indices y contexto.

## Fuera de alcance
- Billing.
- Replanteo arquitectonico.
- Panel admin grande.
- Migraciones de otros dominios no relacionados.
- Eliminacion fisica completa de `AUTH_STORE_FILE`.

## Auditoria resumida previa
- La operacion ya venia endurecida por `TASK_066` y `TASK_067`, con DB preferente para sesiones QR, dispositivos vinculados y auditoria auth/device.
- Seguian faltando reglas operativas cerradas sobre:
  - cual es la fuente de verdad definitiva;
  - como respaldar;
  - que variable/ruta debe tratarse como persistente principal;
  - como recuperar una instancia.
- `TASK_068_auth_device_json_retirement_stage_1.md` fue referenciada por el usuario pero no existe en `docs/02_tasks/`; se documenta como gap previo sin recrear alcance no definido.

## Cierre aplicado
- Se fija como fuente de verdad operativa definitiva para auth/device/session:
  - `operational_device_login_sessions`
  - `operational_linked_devices`
  - `operational_events`
- `AUTH_STORE_FILE` queda formalmente relegado a:
  - compatibilidad transitoria;
  - respaldo auxiliar;
  - fallback controlado.
- Se actualiza `backend/README.md` con:
  - fuente de verdad;
  - rutas persistentes recomendadas;
  - respaldo minimo;
  - recuperacion minima;
  - limites conocidos.
- Se actualiza `backend/.env.example` para remarcar:
  - `AUTH_OPERATIONAL_DB_FILE` como ruta persistente principal;
  - `AUTH_STORE_FILE` como capa auxiliar/transitoria.

## Fuente de verdad definitiva
- Sesiones QR/device login:
  - SQLite `AUTH_OPERATIONAL_DB_FILE`
  - tabla `operational_device_login_sessions`
- Dispositivos vinculados:
  - SQLite `AUTH_OPERATIONAL_DB_FILE`
  - tabla `operational_linked_devices`
- Auditoria auth/device:
  - SQLite `AUTH_OPERATIONAL_DB_FILE`
  - tabla `operational_events`

## Que queda fuera del JSON
- JSON ya no es la fuente operativa principal para:
  - resolver estado de sesion QR;
  - listar dispositivos vinculados;
  - validar revocacion/bloqueo de dispositivo;
  - consultar auditoria auth/device operativa.

## Que papel conserva el JSON
- Compatibilidad transitoria mientras dure la convivencia.
- Respaldo auxiliar del estado historico.
- Fallback controlado cuando SQLite no esta disponible o no devuelve fila util.

## Respaldo minimo
- Respaldar obligatoriamente:
  - `AUTH_OPERATIONAL_DB_FILE`
  - `AUTH_STORE_FILE` mientras siga la convivencia
- Hacer backup al menos:
  - antes de despliegues;
  - antes de cambios manuales relevantes de cuentas/dispositivos;
  - en ventana operativa regular del entorno.

## Recuperacion minima
1. Confirmar `AUTH_OPERATIONAL_DB_ENABLED=true`.
2. Confirmar ruta y permisos de `AUTH_OPERATIONAL_DB_FILE`.
3. Restaurar primero el ultimo backup valido de SQLite.
4. Mantener `AUTH_STORE_FILE` restaurado como capa auxiliar.
5. Reiniciar backend.
6. Validar minimamente:
   - `/health`
   - `/v1/auth/devices`
   - `/v1/auth/device/status/:sessionId` si hay sesion trazable
   - `/v1/auth/ops/accounts/:accountId`

## Archivos tocados
- `backend/README.md`
- `backend/.env.example`
- `docs/02_tasks/TASK_069_auth_device_db_transition_closeout.md`
- `docs/00_index/ACTIVE_TASK.md`
- `docs/00_index/CURRENT_STATUS.md`
- `docs/00_index/CHATGPT_CONTEXT.md`
- `docs/05_changelog/CHANGELOG_2026_Q1.md`

## Riesgos y limites conocidos
- `node:sqlite` sigue siendo experimental en Node.
- No se retiro aun `AUTH_STORE_FILE`; su eliminacion definitiva queda fuera de esta tarea.
- No hay soporte multi-servidor/distribuido para estos dominios.

## Pendiente de prueba manual
- Verificar en entorno real el procedimiento documental de backup + restore de `AUTH_OPERATIONAL_DB_FILE`.

## Resultado esperado
- Auth/device/session queda operativamente sustentado por DB.
- La documentacion deja claro como funciona, como se respalda y que queda definitivamente fuera del JSON.
- El sistema sigue estable y trazable.

## Cambios externos XUI/config requeridos
- No hay cambios externos nuevos para esta tarea.
- Se mantiene pendiente previo fuera del alcance auth/device/session: rotacion/validacion de `XUI_ADMIN_API_KEY`.

## Validacion ejecutada
- Validacion documental de coherencia entre indices + README + changelog.
- `npm test` en `backend/`: OK (`20` tests, `0` fallos).

## Estado de cierre formal
- Objetivo y criterio de exito de `TASK_069` cumplidos.
- `TASK_069_auth_device_db_transition_closeout` cerrada.
