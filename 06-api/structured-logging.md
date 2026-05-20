# Politica de Logging Estructurado

## Objetivo

Definir una politica consistente de logging para el backend del proyecto.

## Reglas adoptadas

- los logs se emiten por `stdout`
- el formato incluye `correlationId` por peticion
- no se deben registrar credenciales, tokens ni cabeceras `Authorization`
- se usan niveles coherentes:
  - `INFO` para eventos operativos normales
  - `WARN` para condiciones recuperables o solicitudes invalidas
  - `ERROR` para fallos inesperados

## Correlation ID

El backend usa el header:

- `X-Correlation-Id`

Comportamiento:

- si el cliente envia `X-Correlation-Id`, el backend lo reutiliza
- si no lo envia, el backend genera uno
- el valor queda disponible en logs mediante MDC
- el backend devuelve el mismo header en la respuesta

## Formato base

La salida de consola incluye:

- timestamp
- nivel de log
- thread
- `corr=<correlationId>`
- logger
- mensaje

## Datos sensibles

No se deben registrar:

- contrasenas
- tokens de autenticacion
- secretos
- cabeceras `Authorization`

## Verificacion local

1. Levantar la API.
2. Enviar una peticion con correlation ID explicito:

```powershell
curl.exe -i -X POST http://localhost:9090/api/auth/login -H "Content-Type: application/json" -H "X-Correlation-Id: demo-correlation-id" -d "{}"
```

3. Confirmar en la respuesta:

- header `X-Correlation-Id: demo-correlation-id`

4. Revisar logs del backend y confirmar:

- aparece `corr=demo-correlation-id`
- no se imprimen contrasenas ni tokens
