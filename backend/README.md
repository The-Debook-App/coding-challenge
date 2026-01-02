# Debook — Backend Coding Challenge (Node.js / NestJS)

Gracias por tu interés en Debook. Este challenge busca evaluar **criterio de ingeniería**.
Queremos ver cómo diseñas un backend **performante**, **mantenible** y **listo para escalar**.

## Contexto

En Debook estamos construyendo una red social alrededor de contenido (posts cortos). Necesitamos un flujo típico de app social: interacción + notificación + buen rendimiento.

## El reto

Implementa una funcionalidad de **interacción** sobre un recurso (por ejemplo: _like_ sobre un post/line) y un flujo de **notificación**.

### Requisitos mínimos

- **NestJS + TypeScript**
- **PostgreSQL** (con migraciones o estrategia clara de schema)
- **TypeORM** (o una alternativa equivalente, pero justifica)
- Al menos **2 endpoints**:
  1. Crear la interacción (ej. `POST /v1/posts/:id/like`)
  2. Obtener el recurso con **contadores** (ej. `GET /v1/posts/:id` devolviendo `likesCount` y otro contador que elijas)
- Debe existir alguna forma de **evitar duplicados** (ej. mismo usuario no puede dar like 2 veces).
- Debe existir un flujo de **notificación asíncrono** cuando ocurre una interacción (puede ser cola/evento/worker; decide tú cómo).
- **Performance**: evita soluciones que carguen relaciones completas para calcular contadores (queremos queries eficientes).
- **Tests**: al menos
  - 1 test unitario relevante (use case / service)
  - 1 test e2e del endpoint de interacción

> Puedes simplificar la autenticación (ej. header `x-user-id` o un guard mock). No hace falta auth real.

## Lo que valoramos (más que “features”)

- Arquitectura clara (controllers delgados, separación de capas, buen naming)
- Decisiones bien justificadas (trade-offs)
- Correctitud (idempotencia o manejo de repetidos, consistencia)
- Buen uso de Postgres (índices, constraints, queries)
- DX (README sencillo, scripts, docker, facilidad para correr)

## Entrega

- Repo con commits (ideal) o zip.
- Incluye:
  - `README` con cómo levantarlo y cómo probarlo
  - `.env.example`
  - `docker-compose.yml` si usas servicios (DB, redis, etc.)
  - Scripts tipo: `start`, `start:dev`, `test`, `test:e2e`

## Tiempo orientativo

No buscamos que sea enorme. Priorizamos **calidad y decisiones** antes que cantidad.

---

Si tienes dudas razonables, decide tú y explícitalo. Preferimos ver tu criterio antes que un challenge 100% guiado.
¡Suerte! 🚀
