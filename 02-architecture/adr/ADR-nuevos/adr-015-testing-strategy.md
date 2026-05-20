# ADR-015 - Estrategia incremental de pruebas automatizadas por capas

## Estado

Aceptado - Miercoles, 20 de mayo 2026

---

# Contexto

El proyecto ya cuenta con evidencia real de pruebas automatizadas en backend y frontend:

* backend con pruebas unitarias sobre servicios
* frontend con pruebas unitarias activas en Karma + Jasmine
* frontend con estructura E2E de Protractor
* comandos de validacion local ya ejecutables y verificados en el repositorio actual

No se adopta en este ADR una politica de cobertura obligatoria ni un pipeline CI/CD especifico, porque esos elementos no forman parte del estado actual confirmado del proyecto.

---

# Problema

Sin una estrategia documentada de pruebas:

* no hay una expectativa comun sobre que capas deben cubrirse
* la calidad depende de decisiones individuales del equipo
* es dificil priorizar que tipos de pruebas mantener o ampliar primero

---

# Decision Arquitectonica

Decision adoptada.

Se adopta una estrategia incremental de pruebas por capas, priorizando:

* pruebas unitarias de servicios backend
* pruebas unitarias de componentes y guards frontend
* validaciones basicas sobre endpoints y comportamientos criticos
* mantenimiento selectivo de flujos E2E realmente utiles

La implementacion actual formaliza un baseline minimo de validacion local:

* backend: `.\mvnw.cmd test`
* frontend: `npm.cmd run lint`
* frontend: `npm.cmd run test -- --watch=false --browsers=ChromeHeadless`
* frontend: `npm.cmd run build`

No se fijan en este ADR umbrales de cobertura, herramientas adicionales ni automatizacion CI/CD no confirmada en el proyecto actual.

---

# Stack tecnologico

| Elemento | Estado actual | Decision adoptada |
|---|---|---|
| Backend tests | JUnit 5 + Mockito via `spring-boot-starter-test` | Mantener y ampliar |
| Frontend unit tests | Karma + Jasmine | Mantener y ampliar |
| Frontend E2E | Protractor presente en el proyecto | Mantener solo escenarios valiosos |
| Baseline local | Comandos reales verificados | Validacion minima oficial |

---

# Diagrama del stack

```
Backend services -> pruebas unitarias
Frontend components/guards -> pruebas unitarias
Endpoints criticos -> validacion basica
Flujos clave usuario -> E2E selectivo
```

---

# Alternativas consideradas

| Alternativa | Por que no se eligio |
|---|---|
| No documentar estrategia | Mantiene dispersion y criterios implicitos |
| Apostar solo por E2E | Aumenta costo y fragilidad para este proyecto |
| Fijar desde ya herramientas y umbrales no presentes | No esta respaldado por evidencia actual |

---

# Beneficios Arquitectonicos

* Ordena el crecimiento de pruebas sin sobredimensionar la solucion
* Alinea pruebas con la arquitectura por capas del proyecto
* Formaliza un baseline de calidad realmente ejecutable

---

# Trade-offs

| Ventaja | Desventaja |
|---|---|
| Estrategia pragmatica y compatible con el estado real del repositorio | No resuelve por si sola cobertura futura o CI/CD |
| Aprovecha lo ya existente | Requiere disciplina para mantener utiles las pruebas E2E |

---

# Impacto en el Sistema

**Backend:** Consolida pruebas unitarias sobre servicios como primera linea de validacion.

**Frontend:** Mantiene pruebas utiles de componentes y guards, con lint y build como parte del baseline.

**Documentacion:** Define una politica clara de validacion local sin depender de cobertura o pipelines no existentes.

---

# Evidencia

* `veterinary-system-api/pom.xml`
* `veterinary-system-api/src/test/java/com/backend/unab/models/services/`
* `veterinary-system-portal/karma.conf.js`
* `veterinary-system-portal/e2e/`
* `veterinary-system-portal/src/app/**/*.spec.ts`
* `veterinary-system-docs/09-testing/testing-strategy.md`

---

# Validacion

* Confirmar que las pruebas backend siguen existiendo en `veterinary-system-api/src/test`
* Confirmar que el frontend mantiene configuracion de Karma/Jasmine y estructura E2E
* Confirmar que el baseline local del proyecto pasa con:
  * `.\mvnw.cmd test`
  * `npm.cmd run lint`
  * `npm.cmd run test -- --watch=false --browsers=ChromeHeadless`
  * `npm.cmd run build`
