# ADR-016 - Logging consistente con correlacion de peticiones

## Estado

Aceptado - Miercoles, 20 de mayo 2026

---

# Contexto

El backend ya usaba el logging por defecto de Spring Boot hacia salida estandar, pero no existia una politica documentada sobre formato, uso de correlation ID ni exclusion explicita de datos sensibles.

---

# Problema

Sin una politica de logging consistente:

* es mas dificil seguir el flujo de una peticion entre varias capas
* el diagnostico de incidentes depende de mensajes dispersos
* existe riesgo de registrar datos sensibles sin una regla clara

---

# Decision Arquitectonica

Decision adoptada.

Se adopta una politica de logging consistente para el backend con estas reglas:

* emitir logs por `stdout`
* usar un formato de consola consistente mediante Logback
* incluir `correlationId` por peticion usando `MDC`
* evitar registrar credenciales, tokens y secretos
* usar niveles de log coherentes (`INFO`, `WARN`, `ERROR`)

La implementacion actual:

* agrega un filtro HTTP para propagar o generar `X-Correlation-Id`
* devuelve ese identificador tambien en la respuesta
* normaliza el formato de consola
* registra errores y eventos criticos sin exponer datos sensibles

El uso de herramientas externas de observabilidad queda fuera del alcance de esta decision.

---

# Stack tecnologico

| Elemento | Estado actual | Decision adoptada |
|---|---|---|
| Backend logging | Spring Boot + Logback | Formato consistente con correlation ID |
| Destino | `stdout` | `stdout` |
| Correlacion | `X-Correlation-Id` por peticion | Implementada |
| Datos sensibles | Sin politica formal previa | Exclusiones explicitamente documentadas |

---

# Diagrama del stack

```
Peticion HTTP
   |
   v
Filtro de correlacion
   |
   +-> MDC correlationId
   +-> header X-Correlation-Id en respuesta
   |
   v
Backend
   |
   +-> logs consistentes por stdout
   +-> sin credenciales ni tokens
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener logging sin lineamientos | Mantiene inconsistencia y menor trazabilidad |
| Definir desde ya una plataforma externa de observabilidad | No esta confirmada en el proyecto actual |
| No considerar correlation ID | Reduce capacidad de seguimiento por peticion |

---

# Beneficios Arquitectonicos

* Mejora mantenimiento y diagnostico
* Reduce riesgo de exponer informacion sensible en logs
* Permite seguir el flujo de una peticion mediante correlation ID

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Lineamientos simples y pragmaticos | Requiere disciplina al escribir logs |
| Correlation ID mejora trazabilidad | Introduce un componente transversal adicional |

---

# Impacto en el Sistema

**Backend:** Normaliza la forma de registrar eventos y errores.

**Seguridad:** Prohibe el registro de credenciales, tokens y secretos.

**Operaciones:** Mantiene `stdout` como salida base sin asumir plataformas externas.

---

# Evidencia

* `veterinary-system-api/src/main/resources/application.properties`
* `veterinary-system-api/src/main/resources/logback-spring.xml`
* `veterinary-system-api/src/main/java/com/backend/unab/config/CorrelationIdFilter.java`
* `veterinary-system-api/src/main/java/com/backend/unab/controllers/AuthRestController.java`
* `veterinary-system-api/src/main/java/com/backend/unab/exception/GlobalExceptionHandler.java`
* `veterinary-system-docs/06-api/structured-logging.md`

---

# Validacion

* Confirmar que el backend incluye `logback-spring.xml`
* Confirmar que el proyecto implementa `X-Correlation-Id` por peticion
* Confirmar que el backend devuelve `X-Correlation-Id` en la respuesta
* Confirmar que logs de error y autenticacion no imprimen credenciales ni tokens
* Confirmar que la salida sigue usando `stdout`
