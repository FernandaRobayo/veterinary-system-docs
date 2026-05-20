# Estrategia de Pruebas

## Alcance

El proyecto adopta una estrategia incremental de pruebas por capas:

- pruebas unitarias de servicios en backend
- pruebas unitarias de componentes y guards en frontend
- validaciones basicas sobre endpoints y comportamientos criticos
- cobertura E2E selectiva solo para flujos con valor real

Esta guia documenta el baseline minimo que hoy ya puede ejecutarse y verificarse en el repositorio.

## Baseline oficial del proyecto

Para considerar validado el baseline definido por ADR-015, deben ejecutarse correctamente estos comandos:

### Backend

Desde `veterinary-system-api`:

```powershell
cd "C:\www\veterinary system\veterinary-system-api"
.\mvnw.cmd test
```

Resultado esperado:

- `BUILD SUCCESS`
- `Tests run: 72`
- `Failures: 0`
- `Errors: 0`

## Frontend

Desde `veterinary-system-portal`:

```powershell
cd "C:\www\veterinary system\veterinary-system-portal"
npm.cmd run lint
npm.cmd run test -- --watch=false --browsers=ChromeHeadless
npm.cmd run build
```

Resultados esperados:

- `npm.cmd run lint`
  - `All files pass linting.`
- `npm.cmd run test -- --watch=false --browsers=ChromeHeadless`
  - `TOTAL: 18 SUCCESS`
- `npm.cmd run build`
  - finaliza correctamente sin error de compilacion
  - puede mostrar advertencias de dependencias antiguas, pero no debe fallar

## Evidencia tecnica actual

### Backend

El backend mantiene pruebas unitarias de servicios en:

- `veterinary-system-api/src/test/java/com/backend/unab/models/services/`

Estas pruebas validan la capa de servicios como primera linea automatizada del proyecto.

### Frontend

El frontend mantiene pruebas unitarias en:

- `veterinary-system-portal/src/app/**/*.spec.ts`

Ademas, conserva configuracion activa de:

- `veterinary-system-portal/karma.conf.js`
- `veterinary-system-portal/e2e/protractor.conf.js`

## Politica E2E

El proyecto todavia contiene estructura E2E con Protractor:

- `veterinary-system-portal/e2e/`

La decision adoptada no obliga a expandir E2E indiscriminadamente. La politica actual es:

- mantener solo escenarios realmente utiles
- evitar duplicar cobertura ya resuelta con unit tests
- preferir pocos flujos estables antes que muchos escenarios fragiles

## Que demuestra este baseline

Si los comandos anteriores pasan, queda demostrado que:

- el backend tiene pruebas automatizadas funcionales
- el frontend mantiene lint y pruebas unitarias ejecutables
- el frontend puede seguir construyendose correctamente
- la estrategia de pruebas de ADR-015 no es solo teórica, sino operativa

## Verificacion actual del ADR-015

La verificacion ya realizada sobre el repositorio actual fue:

- `.\mvnw.cmd test`
- `npm.cmd run lint`
- `npm.cmd run test -- --watch=false --browsers=ChromeHeadless`
- `npm.cmd run build`

Con esos resultados, ADR-015 queda validado en el alcance actual del proyecto.
