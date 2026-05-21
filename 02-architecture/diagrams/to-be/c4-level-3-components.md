# Nivel 3 - Componentes del Backend API (To-Be)

## Descripcion

Esta vista `to-be` amplia el contenedor `Backend API` y documenta los componentes internos mas relevantes del backend objetivo que el equipo desea sostener como referencia tecnica. La imagen asociada muestra tanto la cadena funcional principal como los componentes transversales que soportan seguridad, documentacion, CORS, manejo de errores y correlacion de peticiones.

## Diagrama

- [c4-componentes .jpeg](./c4-componentes%20.jpeg)

## Componentes principales

- `Spring Security Config`
  Configuracion de seguridad stateless y autenticacion `HTTP Basic`.
- `Configuracion CORS`
  Politica CORS explicita y restrictiva por ambiente, centralizada en la configuracion de seguridad.
- `OpenAPI Config`
  Configuracion de documentacion `OpenAPI 3` y `Swagger UI`.
- `CorrelationIdFilter`
  Filtro transversal que propaga `X-Correlation-Id`, lo registra en `MDC` y lo devuelve en la respuesta.
- `GlobalExceptionHandler`
  Manejo global de errores y logging controlado.
- `Controladores REST`
  Capa de entrada que expone la API y delega autenticacion y operaciones CRUD.
- `Servicios de dominio`
  Capa donde reside la logica de negocio principal del sistema veterinario.
- `Repositorios JPA`
  Capa de persistencia basada en `Spring Data JPA`.
- `Base de datos MySQL`
  Persistencia relacional consumida por los repositorios.
- `Flyway migrations`
  Soporte de migraciones versionadas para esquema y datos semilla.

## Relaciones

- `Spring Security Config -> Controladores REST`
  Protege endpoints y aplica autenticacion `HTTP Basic`.
- `Configuracion CORS -> Controladores REST`
  Restringe origenes permitidos por ambiente.
- `OpenAPI Config -> Controladores REST`
  Documenta los endpoints REST expuestos.
- `CorrelationIdFilter -> Controladores REST`
  Propaga el identificador de correlacion por peticion.
- `GlobalExceptionHandler -> Controladores REST`
  Centraliza errores y respuestas controladas.
- `Controladores REST -> Servicios de dominio`
  Delegan autenticacion y operaciones CRUD.
- `Servicios de dominio -> Repositorios JPA`
  Aplican logica de negocio y persistencia.
- `Repositorios JPA -> Base de datos MySQL`
  Acceden a la informacion persistida mediante `JPA/Hibernate`.
- `Flyway migrations -> Base de datos MySQL`
  Versiona y aplica cambios de esquema y datos semilla.

## Ejemplos representativos

- `Controladores REST`
  `AuthRestController`, `CustomerRestController`, `SpeciesRestController`, `BreedRestController`, `PetRestController`, `VeterinarianRestController`, `AppointmentRestController`, `MedicalRecordRestController` y `TreatmentRestController`.
- `Servicios de dominio`
  `UsuarioService`, `CustomerServiceImpl`, `SpeciesServiceImpl`, `BreedServiceImpl`, `PetServiceImpl`, `VeterinarianServiceImpl`, `AppointmentServiceImpl`, `MedicalRecordServiceImpl` y `TreatmentServiceImpl`.
- `Repositorios JPA`
  `IUsuarioDao`, `ICustomerDao`, `ISpeciesDao`, `IBreedDao`, `IPetDao`, `IVeterinarianDao`, `IAppointmentDao`, `IMedicalRecordDao`, `ITreatmentDao` e `IRoleDao`.

## Observaciones

- Esta vista recoge directamente decisiones ya reflejadas en ADR sobre Flyway, CORS, OpenAPI y logging estructurado.
- La API sigue exponiendo endpoints bajo `/api/*`.
- La documentacion OpenAPI permanece disponible mediante `/swagger-ui.html` y `/v3/api-docs`.
- La imagen de `to-be` se conserva como apoyo visual de la estructura objetivo documentada para el backend.
