# ADR-014 - Documentacion formal de la API con OpenAPI 3

## Estado

Aceptado - Martes, 19 de mayo 2026

---

# Contexto

El proyecto expone una API REST bajo `/api/*` y depende de ese contrato para el portal web. Antes de esta decision, la documentacion API del repositorio era insuficiente y no existia una especificacion OpenAPI generada desde el backend.

---

# Problema

Sin documentacion formal de la API:

* los desarrolladores deben inspeccionar controladores y DTOs para entender el contrato
* el onboarding tecnico es mas lento
* no hay una fuente de verdad clara para endpoints, payloads y respuestas

---

# Decision Arquitectonica

Se adopta OpenAPI 3 generado desde el backend con `springdoc-openapi` para documentar la API REST actual del proyecto.

La implementacion:

* mantiene las rutas reales existentes bajo `/api/*`
* expone documentacion navegable con Swagger UI
* publica la especificacion generada en formato JSON y YAML
* documenta autenticacion HTTP Basic como esquema de seguridad del contrato

Esta decision no depende de introducir versionado `/api/v1/*`.

---

# Stack tecnologico

| Elemento | Estado actual |
|---|---|
| Contrato REST | OpenAPI 3 generado desde backend |
| Documentacion API | Swagger UI + `/v3/api-docs` |
| Backend | Spring Boot 2.5.3 + springdoc-openapi |

---

# Diagrama del stack

```
Controladores + DTOs
        |
        v
springdoc-openapi
        |
        +-- /v3/api-docs
        +-- /v3/api-docs.yaml
        +-- /swagger-ui.html
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener documentacion manual dispersa | Se desactualiza con facilidad |
| No documentar formalmente la API | Dificulta mantenimiento y onboarding |
| Amarrar la decision a `/api/v1/*` | No esta justificado por el estado actual del proyecto |

---

# Beneficios Arquitectonicos

* Hace visible el contrato REST existente
* Reduce dependencia de leer directamente el codigo para consumir la API
* Mejora trazabilidad entre backend, frontend y documentacion

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Contrato mas claro y util para el equipo | Introduce dependencia adicional en el backend |
| Mejor soporte para onboarding | Requiere mantener anotaciones y accesos de documentacion alineados con seguridad |

---

# Impacto en el Sistema

**Backend:** Expone la especificacion OpenAPI y Swagger UI, y permite acceso publico a esos endpoints de documentacion.

**Frontend:** Puede consultar un contrato formal de referencia sin cambiar la API actual.

**Documentacion:** Sustituye la dependencia de un archivo vacio o inexistente por una fuente util y trazable.

---

# Evidencia

* `veterinary-system-api/pom.xml`
* `veterinary-system-api/src/main/java/com/backend/unab/config/OpenApiConfig.java`
* `veterinary-system-api/src/main/java/com/backend/unab/auth/SpringSecurityConfig.java`
* `veterinary-system-api/src/main/java/com/backend/unab/controllers/`
* `veterinary-system-docs/06-api/api-documentation.md`

---

# Validacion

* Confirmar que la API sigue exponiendose bajo `/api/*`
* Confirmar que `http://localhost:9090/swagger-ui.html` carga la documentacion navegable
* Confirmar que `http://localhost:9090/v3/api-docs` expone la especificacion OpenAPI
* Confirmar que las pruebas del backend siguen pasando despues de incorporar `springdoc-openapi`
