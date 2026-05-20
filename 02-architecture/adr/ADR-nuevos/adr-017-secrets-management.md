# ADR-017 - Gestion segura de secretos y variables de entorno

## Estado

Aceptado - Miercoles, 20 de mayo 2026

---

# Contexto

El proyecto usa variables de entorno para base de datos y configuracion sensible en los `docker-compose.yml` de sus modulos. Antes de esta decision no existia en la raiz una plantilla comun de variables ni una guia operativa minima para separar valores reales y valores de ejemplo.

La implementacion actual deja en la raiz:

* `.env.example` como plantilla versionada
* `.env` para uso local del entorno de desarrollo
* `.gitignore` para evitar versionar `.env`
* `README-DOCKER.md` con la guia operativa minima

---

# Problema

Sin una politica minima de secretos:

* los valores de `.env` pueden quedar expuestos en el repositorio
* no hay separacion clara entre configuracion local de ejemplo y secretos reales
* el onboarding tecnico carece de una plantilla segura de variables requeridas

---

# Decision Arquitectonica

Decision adoptada.

Se adopta una politica minima e inmediata de gestion de secretos:

**Minimo obligatorio inmediato**

* dejar de tratar `.env` como archivo versionable con valores operativos
* crear `.env.example` con placeholders o valores seguros de desarrollo
* mantener `.env` como archivo local excluido por `.gitignore`
* definir que los valores reales deben inyectarse por ambiente y no conservarse en el repositorio

**Evolucion futura opcional**

* evaluar mecanismos mas robustos de provision de secretos segun el entorno de despliegue real

No se aprueba en este ADR una herramienta externa especifica de secretos porque no esta implementada ni confirmada en el proyecto actual.

---

# Stack tecnologico

| Elemento | Estado actual | Decision adoptada |
|---|---|---|
| Variables sensibles | Variables de entorno en Docker Compose y modulos separados | Plantilla `.env.example` en raiz + `.env` local ignorado + valores reales fuera del repositorio |
| Orquestacion local | Docker Compose | Docker Compose |
| Gestor externo de secretos | No confirmado en el proyecto actual | Queda fuera del alcance actual |

---

# Diagrama del stack

```
Repositorio
   |
   +-> .env.example con placeholders
   |
Entorno local o real
   |
   +-> valores reales inyectados fuera del repositorio
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| Mantener `.env` versionado | Incrementa riesgo de exposicion de secretos |
| No documentar ninguna politica | Mantiene ambiguedad operativa y de seguridad |
| Aprobar desde ya un gestor externo concreto | No esta implementado ni confirmado en el proyecto actual |

---

# Beneficios Arquitectonicos

* Reduce riesgo de exponer credenciales en el repositorio
* Mejora separacion entre configuracion de ejemplo y configuracion real
* Hace mas claro el manejo por ambiente

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Politica inmediata y simple | Requiere cambiar habitos del equipo y del repositorio |
| `.env.example` mejora onboarding | Obliga a mantener sincronizada la lista de variables necesarias |

---

# Impacto en el Sistema

**DevOps:** Debe separar configuracion local de ejemplo y valores reales por ambiente.

**Seguridad:** Reduce exposicion accidental de credenciales.

**Documentacion:** Debe explicar el uso de `.env.example` y evitar presentar `.env` versionado como practica aceptable.

---

# Evidencia

* `docker-compose.yml`
* `.env`
* `.env.example`
* `.gitignore`
* `README-DOCKER.md`
* `veterinary-system-db/.env.example`
* `veterinary-system-db/.gitignore`

---

# Validacion

* Confirmar que existe `.env` en la raiz del proyecto
* Confirmar que existe `.env.example` en la raiz del proyecto
* Confirmar que `.gitignore` en la raiz excluye `.env`
* Confirmar que `README-DOCKER.md` documenta el uso seguro de variables
* Confirmar que `docker compose` raiz sigue delegando en los modulos sin hardcodear secretos reales en documentacion
* Confirmar que `.env` puede usarse localmente sin quedar expuesto en el repositorio
* Confirmar que la plantilla versionada usa placeholders o valores seguros de desarrollo
