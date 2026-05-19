# ADR-010 - Migracion a Flyway para gestion versionada del esquema de base de datos

## Estado

Aceptado - Lunes, 18 de mayo 2026

---

# Contexto

Actualmente el esquema de base de datos se gestiona mediante `import.sql` y `auth-seed.sql`, ejecutados al arrancar la aplicacion mediante configuracion Spring.

Este mecanismo sirve para bootstrap local y entornos simples, pero no ofrece trazabilidad historica de cambios ni una evolucion incremental del esquema.

---

# Problema

Con scripts SQL de inicializacion como unico mecanismo de gestion de esquema:

* no existe historial versionado de cambios estructurales
* no hay un mecanismo estandar para aplicar solo cambios pendientes
* el mantenimiento del esquema depende de reemplazar o editar scripts base
* es mas dificil evolucionar la base en entornos persistentes

---

# Decision Arquitectonica

Decision adoptada.

Se adopta Flyway como herramienta de migraciones versionadas para que la evolucion del esquema quede trazada en el repositorio y desacoplada de los scripts de bootstrap inicial.

Con esta decision:

* el proyecto deja de depender de `import.sql` y `auth-seed.sql`
* Flyway pasa a formar parte del stack actual para la gestion versionada del esquema

---

# Stack tecnologico

| Elemento | Estado actual | Decision adoptada |
|---|---|---|
| Inicializacion de esquema | Flyway | Flyway |
| Inicializacion de datos auth | Migraciones versionadas o seed controlado | Migraciones versionadas o seed controlado |
| ORM | Spring Data JPA / Hibernate | Spring Data JPA / Hibernate |
| Motor de base de datos | MySQL 8.0 | MySQL 8.0 |

---

# Diagrama del stack

```
Backend -> Flyway migrations -> MySQL
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener solo `import.sql` y `auth-seed.sql` | No ofrece trazabilidad ni evolucion incremental |
| Migraciones manuales sin herramienta | Depende de procesos informales y es mas dificil de auditar |
| Liquibase | Requiere otra curva de adopcion; no esta justificado por evidencia adicional en el proyecto actual |

---

# Beneficios Arquitectonicos

* Versionado claro de cambios de esquema
* Mejor trazabilidad entre codigo y base de datos
* Menor riesgo al evolucionar entornos que ya tienen datos

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Formaliza la evolucion del esquema | Requiere migrar los scripts actuales a un nuevo formato de trabajo |
| Facilita trazabilidad | Introduce una herramienta adicional al proyecto |

---

# Impacto en el Sistema

**Backend:** Incorpora Flyway y reorganiza la inicializacion de esquema.

**Base de datos:** Cambia la forma de evolucionar el esquema, no el motor relacional.

**Documentacion:** Debe reflejar que Flyway ya es el mecanismo adoptado para versionar el esquema.

---

# Evidencia

* `veterinary-system-db/db/migration/V0001__initial_schema.sql`
* `veterinary-system-db/db/migration/V0002__auth_seed.sql`
* `veterinary-system-db/db/migration/V0003__extended_seed_data.sql`
* `veterinary-system-db/docker-compose.yml`
* `veterinary-system-db/db/CHANGELOG_DB.md`
* `veterinary-system-api/src/main/resources/application.properties`

---

# Validacion

* Confirmar la existencia de `flyway_schema_history` en la base de datos
* Confirmar que las migraciones viven en `veterinary-system-db/db/migration`
* Confirmar que `import.sql` y `auth-seed.sql` ya no existen en `veterinary-system-api/src/main/resources`
* Confirmar que el servicio `flyway` esta disponible en `veterinary-system-db/docker-compose.yml`
