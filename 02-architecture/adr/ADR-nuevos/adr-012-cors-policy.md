# ADR-012 - Politica CORS explicita y restrictiva por ambiente

## Estado

Aceptado - Martes, 19 de mayo 2026

---

# Contexto

El backend y el frontend pueden ejecutarse en origenes distintos durante desarrollo local.

Antes de esta implementacion, la politica CORS observada era permisiva:

* `SpringSecurityConfig` permite `allowedOrigins("*")`
* los controladores revisados usan `@CrossOrigin(origins = "*")`

En un despliegue futuro same-origin, por ejemplo frontend publicado con una ruta publica que resuelve `/api/*`, la necesidad de CORS cambia. Por eso la politica debe ser explicita y dependiente del ambiente.

---

# Problema

Sin una politica CORS documentada y controlada por ambiente:

* el backend puede quedar innecesariamente abierto a origenes arbitrarios
* se mezclan necesidades de desarrollo local con despliegues reales
* la seguridad del acceso desde navegador depende de defaults permisivos

---

# Decision Arquitectonica

Se propone definir una politica CORS explicita y parametrizable por ambiente:

* desarrollo local: permitir solo los origenes necesarios del frontend local
* despliegues same-origin: reducir o eliminar la necesidad de CORS si frontend y backend comparten dominio publico
* otros despliegues: permitir solo origenes explicitamente aprobados

La implementacion adoptada centraliza CORS en `SpringSecurityConfig`, elimina `@CrossOrigin(origins = "*")` de los controladores y externaliza la lista de origenes permitidos mediante propiedad de aplicacion.

---

# Stack tecnologico

| Elemento | Estado actual | Implementacion adoptada |
|---|---|---|
| CORS en seguridad | Configuracion centralizada | Lista explicita por ambiente |
| CORS en controladores | Sin `@CrossOrigin(origins = "*")` | Uso centralizado |
| Despliegue frontend/backend | Local con origenes distintos; futuro same-origin posible | Configuracion segun ambiente |

---

# Diagrama del stack

```
Desarrollo local:
Frontend local -> Backend con CORS permitido para origenes necesarios

Despliegue same-origin:
Frontend publico -> /api/* -> Backend
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener `*` en todos los ambientes | Es demasiado permisivo para despliegues reales |
| No configurar CORS explicitamente | Mantiene ambiguedad y dependencia del estado actual del codigo |
| Resolver CORS solo en infraestructura | No reemplaza la necesidad de una politica clara en el backend |

---

# Beneficios Arquitectonicos

* Alinea seguridad del navegador con el entorno real
* Evita configurar permisos mas amplios de los necesarios
* Separa correctamente desarrollo local de despliegues reales

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Menor superficie expuesta desde navegador | Requiere mantener configuracion por ambiente |
| Politica mas clara y auditable | Necesita revisar tanto configuracion central como anotaciones en controladores |

---

# Impacto en el Sistema

**Backend:** Requiere revisar `SpringSecurityConfig` y el uso de `@CrossOrigin`.

**Frontend:** Sin cambio funcional directo; solo debe operar desde origenes permitidos.

**DevOps:** Debe definir origenes segun ambiente cuando aplique.

---

# Evidencia

* `veterinary-system-api/src/main/java/com/backend/unab/auth/SpringSecurityConfig.java`
* `veterinary-system-api/src/main/java/com/backend/unab/controllers/AuthRestController.java`
* `veterinary-system-api/src/main/java/com/backend/unab/controllers/CustomerRestController.java`
* `veterinary-system-api/src/main/resources/application.properties`
* `veterinary-system-api/docker-compose.yml`
* `veterinary-system-portal/nginx/default.conf`
* `veterinary-system-portal/src/app/utils/api-url.ts`

---

# Validacion

* Confirmar que `SpringSecurityConfig.java` ya no use `allowedOrigins("*")`
* Confirmar que los controladores ya no usen `@CrossOrigin(origins = "*")`
* Confirmar que los origenes permitidos se definan por propiedad `app.cors.allowed-origins`
* Validar que el portal local siga operando desde `http://localhost:3000`
