# CURRENT_STATUS

## Estado general
- El primer bridge minimo ya fue validado en TV fisica.
- La TV app carga Home desde backend y muestra `test1`.
- La TV app solicita playback via backend y reproduce stream real de XUI.

## Resultado de la fase actual
- Flujo validado: Home -> seleccion de item -> playback.
- Arquitectura confirmada: `App TV -> Backend propio -> XUI`.
- Ajuste tecnico aplicado: `android:usesCleartextTraffic="true"` para backend LAN en HTTP.

## Dependencias externas actuales
- Backend debe mantenerse levantado en `10.10.6.121:8080` durante pruebas locales.
- En XUI, el stream de prueba puede requerir restart manual cuando se pausa/cae.

## Siguiente enfoque recomendado
- Estabilizacion operativa del stream (health check/reintento) en siguiente tarea.
