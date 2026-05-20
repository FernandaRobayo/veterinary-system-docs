# ADR-011 - HTTPS obligatorio y terminacion TLS desacoplada del despliegue base

## Estado

Aceptado - Miercoles, 20 de mayo 2026

---

# Contexto

El sistema usa HTTP Basic Authentication. En ese mecanismo las credenciales se transportan en Base64, por lo que requieren un canal cifrado para no quedar expuestas durante el trafico real.

El repositorio mantiene un despliegue base local con Docker Compose en HTTP para desarrollo cotidiano. Al mismo tiempo, el proyecto necesitaba validar tecnicamente la terminacion TLS sin contaminar esa configuracion base con certificados autofirmados, puertos locales fijos o reglas de redireccion no aptas como configuracion por defecto.

---

# Problema

Sin una separacion clara entre despliegue base y validacion TLS:

* las credenciales HTTP Basic quedan expuestas si se reutiliza HTTP en trafico real
* una prueba local con certificados autofirmados puede terminar acoplada a la configuracion principal del proyecto
* se incrementa el riesgo de arrastrar `localhost`, puertos locales o certificados no confiables a futuros despliegues

---

# Decision Arquitectonica

Decision adoptada.

Se adopta la siguiente estrategia:

* el despliegue base del portal permanece en HTTP para desarrollo local
* cualquier validacion local de TLS debe vivir en archivos separados del despliegue base
* la terminacion TLS se considera una responsabilidad del punto de entrada del entorno real, no del `docker-compose.yml` base del portal
* la configuracion autofirmada se acepta solo como mecanismo de prueba tecnica local del ADR

Con esta decision:

* el proyecto valida tecnicamente terminacion TLS sin forzarla como configuracion principal
* el repositorio evita acoplar el despliegue base a `localhost`, `3443` o certificados no confiables
* el portal queda preparado para que un entorno real provea HTTPS sin heredar defaults inseguros desde la configuracion local

---

# Stack tecnologico

| Elemento | Estado actual | Decision adoptada |
|---|---|---|
| Autenticacion | HTTP Basic | HTTP Basic con requerimiento de canal cifrado fuera del modo base local |
| Frontend local base | Angular servido con Nginx por HTTP | Sin TLS por defecto |
| Validacion local TLS | Override aislado con certificado autofirmado | Solo para prueba tecnica |
| Configuracion principal del portal | Docker Compose base + `default.conf` | Desacoplada de certificados y puertos TLS locales |

---

# Diagrama del stack

```
Modo base local:
Usuario -> HTTP -> Nginx local -> Frontend + proxy /api/*

Validacion local TLS:
Usuario -> HTTPS -> Nginx local con override aislado -> Frontend + proxy /api/*
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener HTTP como unica opcion incluso para validacion tecnica | No permite ensayar terminacion TLS |
| Dejar TLS autofirmado dentro del compose base | Acopla el proyecto a certificados no confiables y puertos locales |
| Definir un unico terminador TLS fijo dentro del repo base | Mezcla necesidades de prueba local con despliegues reales |

---

# Beneficios Arquitectonicos

* Reduce riesgo de contaminar el despliegue base con configuracion local de TLS
* Permite validar tecnicamente HTTPS sin comprometer la configuracion principal
* Mantiene el proyecto preparado para que el entorno real asuma terminacion TLS de forma limpia

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| La configuracion base queda limpia y portable | La validacion TLS requiere un compose override adicional |
| Se evita arrastrar defaults locales inseguros | La prueba local con autofirmado no representa por si sola un despliegue real |

---

# Impacto en el Sistema

**Frontend:** Mantiene un modo base HTTP local y un modo TLS separado solo para validacion.

**Backend:** Sigue detras del proxy local; no asume TLS directo desde la aplicacion.

**DevOps:** Debe mantener cualquier configuracion TLS de prueba fuera del compose base.

**Seguridad:** Establece que la validacion local de TLS no debe confundirse con la configuracion principal del proyecto.

---

# Evidencia

* `veterinary-system-api/src/main/java/com/backend/unab/auth/SpringSecurityConfig.java`
* `veterinary-system-api/src/main/java/com/backend/unab/controllers/AuthRestController.java`
* `veterinary-system-portal/docker-compose.yml`
* `veterinary-system-portal/docker-compose.local-https.yml`
* `veterinary-system-portal/nginx/default.conf`
* `veterinary-system-portal/nginx/default.local-ssl.conf`
* `veterinary-system-portal/scripts/generate-local-cert.ps1`
* `veterinary-system-portal/README.md`

---

# Validacion

* Confirmar que el `docker-compose.yml` base del portal solo publica HTTP y no monta certificados
* Confirmar que `default.conf` no declara `listen 443` ni rutas de certificado
* Confirmar que la validacion TLS vive en `docker-compose.local-https.yml` y `default.local-ssl.conf`
* Confirmar que HTTP Basic sigue siendo el mecanismo actual de autenticacion
* Confirmar que la prueba local de TLS puede ejecutarse sin modificar la configuracion principal del portal
