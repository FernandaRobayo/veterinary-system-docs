# Nivel 2 - Contenedores (To-Be)

## Descripcion

Esta vista `to-be` describe la descomposicion del sistema en contenedores principales segun la arquitectura objetivo documentada por el equipo. La imagen asociada sintetiza el frontend, el backend, la base de datos y la gestion de migraciones como piezas separadas dentro del sistema.

## Diagrama

- [C4-contenedores .jpeg](./C4-contenedores%20.jpeg)

## Elementos

- `Usuario interno de la veterinaria`
  Actor principal que interactua con el sistema desde un navegador.
- `Frontend web`
  Contenedor web basado en `Angular 10 + Nginx`.
- `Backend API`
  API REST implementada con `Spring Boot 2.5.3 + Java 11`.
- `Base de Datos MySQL`
  Contenedor de persistencia relacional con `MySQL 8.0`.
- `Flyway migrations`
  Contenedor o componente de soporte encargado de aplicar migraciones de esquema y datos iniciales.

## Relaciones

- `Usuario interno de la veterinaria -> Frontend web`
  Usa la interfaz principal del sistema desde el navegador.
- `Frontend web -> Backend API`
  Consume endpoints `/api/*` por `HTTP/JSON`.
- `Backend API -> Base de Datos MySQL`
  Lee y escribe datos del dominio para autenticacion y gestion veterinaria.
- `Flyway migrations -> Base de Datos MySQL`
  Aplica migraciones versionadas del esquema y datos iniciales.

## Decisiones reflejadas en la vista

- El frontend permanece separado del backend como SPA servida por Nginx.
- El backend concentra la logica de negocio y exposicion de la API REST.
- La persistencia relacional se mantiene en MySQL.
- La gestion del esquema deja de depender de archivos SQL cargados manualmente en recursos y se formaliza mediante Flyway.

## Observaciones

- Esta vista es coherente con la separacion de responsabilidades establecida por los ADR de base de datos, CORS, OpenAPI y logging.
- La imagen almacenada en `to-be` se usa como referencia grafica de la estructura objetivo documentada.
- Si mas adelante se desea modelar infraestructura cloud, esa evolucion debe representarse en otra vista `to-be` mas especifica y no en esta descripcion basica de contenedores.
