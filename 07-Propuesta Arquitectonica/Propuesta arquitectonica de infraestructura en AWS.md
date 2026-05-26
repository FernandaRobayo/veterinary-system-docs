# Propuesta arquitectonica de infraestructura en AWS

## 1. Objetivo de la propuesta

La infraestructura propuesta tiene como finalidad desplegar el Sistema Veterinario Web en servicios de AWS, separando la capa de presentacion, la capa de backend y la capa de datos. Esta separacion permite una arquitectura mas organizada, escalable y mantenible para el proyecto.

La solucion usa servicios administrados y de bajo costo operativo:

- `Amazon S3` para alojar el frontend estatico
- `Amazon CloudFront` como punto de entrada HTTPS y distribucion de contenido
- `Amazon EC2` para ejecutar el backend contenerizado con Docker
- `Amazon RDS` para administrar la base de datos relacional MySQL

Amazon S3 permite alojar contenido estatico como `HTML`, `CSS`, `JavaScript` y `assets` del frontend, mientras que CloudFront actua como CDN para entregar contenido y APIs con baja latencia desde ubicaciones perimetrales.

## 2. Servicios AWS utilizados

| Servicio AWS | Uso dentro del proyecto |
|---|---|
| `Amazon S3` | Almacena los archivos compilados del frontend Angular: `index.html`, `main.js`, `styles.css`, `assets`, entre otros. |
| `Amazon CloudFront` | Publica el frontend mediante HTTPS y enruta las peticiones `/api/*` hacia el backend en EC2. |
| `Amazon EC2` | Ejecuta el backend Spring Boot dentro de un contenedor Docker expuesto internamente por el puerto `9090`. |
| `Amazon RDS MySQL` | Aloja la base de datos relacional `db_unab` usada por el sistema veterinario. |
| `Security Groups` | Controlan el trafico de entrada y salida hacia EC2 y RDS. |
| `IAM` | Administra permisos de acceso a la consola y recursos de AWS para usuarios administradores o evaluadores. |

Amazon EC2 permite ejecutar servidores virtuales en la nube, mientras que Amazon RDS facilita la creacion, operacion y escalamiento de bases de datos relacionales administradas en AWS. Los Security Groups funcionan como reglas de control de trafico de entrada y salida para los recursos asociados. IAM permite administrar permisos mediante politicas asociadas a usuarios, grupos, roles o recursos.

## 3. Arquitectura propuesta

```text
Usuario / Navegador
        |
        | HTTPS
        v
Amazon CloudFront
        |
        |-------------------------------
        |                              |
        | /*                           | /api/*
        v                              v
Amazon S3                      Amazon EC2
Frontend Angular               Backend Spring Boot + Docker
                                       |
                                       | JDBC / MySQL 3306
                                       v
                              Amazon RDS MySQL
                              Base de datos db_unab
```

## 4. Descripcion de la arquitectura

El usuario accede al sistema mediante la URL publica de CloudFront:

`https://douruok6d9cq6.cloudfront.net`

CloudFront funciona como el punto de entrada principal de la aplicacion. Para las rutas del frontend, CloudFront entrega los archivos estaticos almacenados en el bucket S3:

`veterinary-system-portal-robayo-20260520`

Para las rutas de backend, se configuro un comportamiento especifico en CloudFront con el patron:

`/api/*`

Este comportamiento redirige las peticiones hacia el backend ejecutado en EC2:

`http://44.204.115.36:9090`

De esta forma, el navegador siempre consume la aplicacion usando HTTPS desde CloudFront, evitando errores de Mixed Content. La comunicacion visible para el usuario queda centralizada en:

`https://douruok6d9cq6.cloudfront.net/api/...`

Internamente, CloudFront reenvia las peticiones al backend Spring Boot desplegado en Docker sobre EC2.

## 5. Capa de presentacion: Amazon S3 + CloudFront

El frontend Angular se compila localmente y genera una carpeta `dist/frontend-unab` con archivos estaticos. Estos archivos se cargan en el bucket S3 del proyecto.

El bucket S3 contiene archivos como:

- `index.html`
- `main.<hash>.js`
- `runtime.<hash>.js`
- `polyfills.<hash>.js`
- `styles.<hash>.css`
- `scripts.<hash>.js`
- `assets/`

CloudFront consume ese bucket como origen principal y entrega la aplicacion al usuario final mediante HTTPS. Esto mejora la disponibilidad del frontend y evita depender de un servidor web tradicional para servir archivos estaticos.

## 6. Capa de backend: Amazon EC2 + Docker

El backend del sistema veterinario se despliega en una instancia EC2 con Ubuntu. Sobre esta instancia se ejecuta Docker, y dentro de Docker se levanta la imagen:

`veterinary-system-api:latest`

El contenedor se expone en el puerto:

`9090`

La API se valido mediante la ruta de autenticacion:

`https://douruok6d9cq6.cloudfront.net/api/auth/login`

y mediante la documentacion tecnica:

`https://douruok6d9cq6.cloudfront.net/v3/api-docs`

La ejecucion mediante Docker facilita empaquetar el backend con sus dependencias, permitiendo repetir el despliegue de forma mas controlada.

## 7. Capa de datos: Amazon RDS MySQL

La base de datos se aloja en Amazon RDS con motor MySQL. El backend se conecta a RDS mediante JDBC usando el endpoint configurado:

`veterinary-system-db.cqjqws2ymsp8.us-east-1.rds.amazonaws.com:3306/db_unab`

RDS centraliza la administracion de la base de datos y evita instalar MySQL directamente en la instancia EC2. Esto mejora la separacion de responsabilidades: EC2 ejecuta logica de negocio y RDS gestiona persistencia.

## 8. Seguridad de red

La seguridad de red se controla mediante Security Groups:

| Recurso | Regla principal |
|---|---|
| `EC2 backend` | Puerto `22` para SSH restringido a la IP autorizada del administrador. |
| `EC2 backend` | Puerto `9090` habilitado para recibir trafico HTTP del backend. |
| `RDS MySQL` | Puerto `3306` habilitado para permitir conexion desde la instancia o backend autorizado. |

Para una version mas segura de produccion, se recomienda que el puerto `9090` no quede abierto publicamente, sino restringido al origen de CloudFront o reemplazado por un Load Balancer con HTTPS.

## 9. Flujo de despliegue

El flujo de despliegue definido para el frontend es:

1. Corregir codigo Angular.
2. Compilar el frontend.
3. Verificar que no exista referencia directa a `:9090`.
4. Subir el contenido de `dist/frontend-unab` al bucket S3.
5. Invalidar cache en CloudFront.
6. Validar la aplicacion desde navegador.

Comando usado para compilar:

```bash
node --openssl-legacy-provider ./node_modules/@angular/cli/bin/ng build --prod
```

Comando de validacion local:

```powershell
findstr /S /N /I ":9090" dist\frontend-unab\*.js
```

La salida esperada es vacia, lo cual indica que el frontend ya no tiene quemada la URL directa del backend.

## 10. Beneficios de la propuesta

| Beneficio | Descripcion |
|---|---|
| `Separacion por capas` | Frontend, backend y base de datos estan separados en servicios distintos. |
| `Mejor seguridad web` | El usuario consume la aplicacion por HTTPS mediante CloudFront. |
| `Menor acoplamiento` | El frontend no llama directamente a la IP publica de EC2. |
| `Escalabilidad inicial` | El frontend puede escalar mediante CloudFront y S3 sin aumentar carga sobre EC2. |
| `Despliegue controlado` | El backend se ejecuta en contenedor Docker. |
| `Persistencia administrada` | La base de datos se ejecuta en RDS en lugar de instalarse manualmente en EC2. |
| `Evidencia tecnica clara` | Swagger/OpenAPI permite demostrar el funcionamiento del backend. |

## 11. Validacion de la infraestructura

La infraestructura se valido con las siguientes pruebas:

| Validacion | Resultado esperado |
|---|---|
| `Acceso al frontend` | `https://douruok6d9cq6.cloudfront.net/login` carga correctamente. |
| `Login por API` | `POST /api/auth/login` responde `HTTP 200`. |
| `Backend activo` | El contenedor `veterinary-system-api` aparece en ejecucion. |
| `Conexion backend-RDS` | Spring Boot inicia correctamente el pool de conexiones Hikari. |
| `Eliminacion de Mixed Content` | El frontend consume `/api/*` por HTTPS mediante CloudFront. |
| `Validacion OpenAPI` | `/v3/api-docs` responde documentacion JSON de la API. |

## 12. Conclusion

La propuesta de infraestructura en AWS implementa una arquitectura web distribuida y funcional para el Sistema Veterinario Web. La solucion separa el frontend, backend y base de datos mediante servicios especializados de AWS: S3 para archivos estaticos, CloudFront para entrega HTTPS y enrutamiento, EC2 para la ejecucion del backend contenerizado y RDS para la persistencia relacional.

Esta arquitectura permite presentar una solucion funcional, desplegada y verificable en la nube, manteniendo una base tecnica adecuada para futuras mejoras como dominio propio, certificado SSL personalizado, Load Balancer, monitoreo con CloudWatch, politicas IAM mas estrictas y automatizacion del despliegue con CI/CD.
