# TASK_001_initial_app_audit

## Objetivo
Reorganizar la base del repositorio a estructura monorepo y auditar técnicamente la app TV existente sin romper comportamiento funcional ni implementar integración real con XUI.

## Alcance
- Movimiento de la app TV existente a `apps/tv-app/`.
- Creación base de `apps/web-app/` y `backend/`.
- Generación de documentación base en `docs/`.
- Auditoría técnica de la app TV actual (estado as-is).

## Archivos tocados (alto nivel)
- Estructura movida: `app/tv-app/` -> `apps/tv-app/`.
- Nuevos README: `apps/web-app/README.md`, `backend/README.md`.
- Índices y documentación base en `docs/`.

## Auditoría técnica de `apps/tv-app/`

### 1) Tecnología base
- Proyecto Android TV con Gradle Kotlin DSL, módulos por features/libs y Hilt.
- UI con Jetpack Compose TV.

### 2) Lenguaje / framework
- Lenguaje principal: Kotlin.
- Framework UI: Compose + Navigation Compose.
- DI: Dagger Hilt.

### 3) Estructura principal
- Módulo app principal: `app/`.
- Módulos de features: `features/login`, `features/config`.
- Librerías internas: `libs/content`, `libs/network`, `libs/player`, `libs/exoplayer`, `libs/auth`, `libs/auth-imp`, etc.

### 4) Entry points
- Android `MainActivity` como launcher TV.
- `MainApp` inicializa Hilt y logging.

### 5) Navegación de la app
- Navegación central en `navigation/AppNavigation.kt` con `NavHost`.
- Rutas principales: login, token login demo, perfiles (who is watching), home, detalle, player, mp3.
- Start destination actual: `home_screen`.

### 6) Dónde están los mocks
- Catálogo demo principal en `app/src/.../features/DemoCatalogRepository.kt`.
- Proveedor fake de películas en `libs/content/.../FakeDataProvider.kt`.
- Proveedor fake de cast en `libs/content/.../FakeCastProvider.kt`.

### 7) Cómo se representa el contenido mock
- DTOs de películas y videos en `libs/content/data/*Response.kt`.
- `DemoCatalogRepository` arma secciones, búsqueda, favoritos y URLs demo de reproducción.
- `FakeMoviesService` responde datos locales simulados implementando `MoviesService`.

### 8) Partes que parecen login/demo/perfiles
- Login email/password en `features/login/withEmailPassword` (flujo demo).
- Login por token/dispositivo en `features/login/withToken` con token hardcodeado (`"OTF2"`) y URL demo.
- Selección de perfiles en `features/wiw` (Who Is Watching), también de naturaleza demo.

### 9) Cómo reproduce video
- `PlayerScreen` usa `PlayerFactory` y `TLPlayer` (abstracción de player).
- Implementación actual con Media3 ExoPlayer en `libs/exoplayer`.
- URL de reproducción tomada de `DemoCatalogRepository.playbackUrlForId(...)` con lista de videos públicos demo.

### 10) Capa de servicios/repositorios/api
- Existe capa de servicio `MoviesService` con dos implementaciones: TMDB y fake.
- Existe `MoviesRepository` con `RemoteMoviesDataSource` y `LocalMoviesDataSource`.
- Estado actual DI: los data sources están cableados a `@Named("FakeMoviesService")`, por lo que la ejecución se mantiene en mock.

### 11) Almacenamiento local
- Sí, existe almacenamiento local de sesión con DataStore Preferences en `libs/auth-imp/DefaultUserSession.kt`.
- Persiste user id, nombre, email, access token, refresh token y expiración.

### 12) Identificación de dispositivo
- No se observó identificación real de dispositivo persistida/gestionada (ej. Android ID/UUID propio).
- En login por token hay token estático de demo, no vinculación técnica real con backend.

### 13) Riesgos técnicos para integrar luego con XUI
- Riesgo de acoplamiento a estructuras demo (`DemoCatalogRepository`) en pantallas principales.
- Contrato de dominio de contenido aún orientado a película demo/TMDB, no a contrato IPTV/XUI final.
- Falta de capa backend intermedia operativa para cumplir arquitectura objetivo.
- Navegación inicia en home aunque existen flujos login/token/perfil; esto puede ocultar requisitos reales de sesión.

### 14) Recomendaciones para integración mínima futura sin romper app
- Mantener UI y navegación actuales, introduciendo una **capa de gateway/adapter** detrás de repositorios.
- Sustituir gradualmente fuentes fake por un endpoint de backend propio manteniendo modelos de UI estables.
- Incorporar feature flag/env para cambiar proveedor de contenido (mock vs backend) sin refactor masivo.
- Definir contrato mínimo backend para: catálogo home, detalle de contenido y URL de playback.
- Agregar identificador de dispositivo controlado por backend antes de login real.

## Riesgos
- Riesgo bajo de ruta/automatización externa: scripts CI/CD externos que asumían `app/tv-app` deberán actualizarse a `apps/tv-app`.
- Riesgo moderado de deuda técnica por coexistencia de capa TMDB/fake y repositorio demo en `app/`.

## Pendiente de prueba por el usuario
- Validar manualmente que la estructura del repositorio queda alineada con lo esperado.
- Confirmar que el proyecto TV se reconoce y compila desde `apps/tv-app/`.
- Revisar consistencia documental para arranque de siguientes fases.

## Resultado esperado
- Base monorepo ordenada, sin romper lógica funcional existente.
- Diagnóstico técnico inicial documentado para planificar integración con backend/XUI en siguientes tareas.

## Cambios manuales externos requeridos
- Ninguno en esta fase.
