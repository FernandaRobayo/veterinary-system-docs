# ADR-013 - Estrategia de despliegue de bajo costo para el frontend en AWS

## Estado

Aceptado - Martes, 19 de mayo 2026

---

# Contexto

El frontend es una SPA Angular que en el entorno local actual se construye y publica con Nginx dentro de un contenedor.

El proyecto ya permite compilar el frontend como artefacto estatico y operar en produccion con base relativa para la API, lo que habilita una estrategia de despliegue de menor costo en AWS basada en sitio estatico.

---

# Problema

Si el frontend se despliega en AWS manteniendo una estrategia contenedorizada obligatoria:

* pueden introducirse costos operativos innecesarios para una SPA estatica
* se sobredimensiona la infraestructura de publicacion del frontend
* se pierde la ventaja de separar el frontend estatico del backend

---

# Decision Arquitectonica

Para un despliegue futuro del frontend en AWS, la opcion preferente del proyecto sera:

* compilar Angular como artefacto estatico
* publicar los archivos estaticos en S3
* usar CloudFront como distribucion y punto de entrega
* resolver `/api/*` hacia el backend desde la capa de infraestructura o usar configuracion equivalente

Nginx en contenedor se mantiene como alternativa local o de despliegue contenedorizado, pero no como requisito obligatorio para produccion AWS.

La implementacion actual deja al frontend preparado para esta estrategia:

* `environment.prod.ts` no fija un host absoluto para la API
* `api-url.ts` permite usar `/api/*` en produccion
* `README.md` del portal documenta `S3 + CloudFront` como opcion preferente en AWS
* `Dockerfile` y `docker-compose.yml` se conservan para desarrollo local y despliegues contenedorizados alternativos

Esta decision no responde a un requerimiento academico explicito, sino a una decision tecnica del proyecto orientada a reducir costos operativos al momento de desplegar en AWS.

---

# Stack tecnologico

| Elemento | Estado actual | Implementacion adoptada |
|---|---|---|
| Frontend | Angular 10 | Angular 10 |
| Publicacion local | Nginx en contenedor | Sin cambio obligatorio |
| Publicacion AWS de bajo costo | Preparada y documentada | S3 + CloudFront |
| Resolucion API en produccion | Base relativa preparada | `/api/*` resuelto por infraestructura equivalente |

---

# Diagrama del stack

```
Local actual:
Angular build -> Nginx container -> navegador

Propuesta futura AWS:
Angular build -> S3 + CloudFront -> navegador
                         |
                         +-> /api/* hacia backend o equivalente
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener Nginx contenedorizado como unica opcion | Sobredimensiona el frontend para una SPA estatica |
| Publicar frontend con host absoluto fijo hacia backend | Reduce flexibilidad y acopla el build al entorno |
| No documentar una estrategia futura | Deja ambigua una decision de despliegue ya insinuada en el proyecto |

---

# Beneficios Arquitectonicos

* Reduce costos potenciales de publicacion del frontend
* Aprovecha que Angular genera artefactos estaticos
* Mantiene separacion entre despliegue local y despliegue productivo futuro

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Menor costo operativo potencial en AWS | Requiere configurar correctamente routing SPA y resolucion de `/api/*` |
| Menor dependencia de contenedores para el frontend | Introduce una variante adicional de despliegue a documentar |

---

# Impacto en el Sistema

**Frontend:** No cambia la aplicacion Angular; cambia la estrategia de publicacion futura.

**Backend:** Debe poder resolverse detras de `/api/*` o configuracion equivalente en produccion.

**DevOps:** Mantiene Docker Compose para local y habilita una alternativa futura de menor costo en AWS.

**Documentacion:** Debe dejar claro que se trata de una decision tecnica del proyecto, no de un requisito academico.

**Operacion AWS:** Debe configurarse correctamente el fallback de la SPA a `index.html` y la resolucion separada de `/api/*`.

---

# Evidencia

* `veterinary-system-portal/README.md`
* `veterinary-system-portal/src/environments/environment.prod.ts`
* `veterinary-system-portal/src/app/utils/api-url.ts`
* `veterinary-system-portal/Dockerfile`
* `veterinary-system-portal/nginx/default.conf`

---

# Validacion

* Confirmar que `environment.prod.ts` no defina host absoluto para la API
* Confirmar que `api-url.ts` permita usar base relativa en produccion
* Confirmar que la opcion `S3 + CloudFront` este documentada en el proyecto
* Confirmar que el build de produccion del frontend se genere correctamente
* La implementacion final en AWS requiere definir la infraestructura exacta que resolvera `/api/*`
