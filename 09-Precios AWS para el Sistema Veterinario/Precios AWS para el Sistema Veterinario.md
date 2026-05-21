# Precios AWS para el Sistema Veterinario

## 1. Objetivo

Este documento registra informacion de **precios AWS** aplicable al posible despliegue del Sistema Veterinario.

El enfoque de este archivo es estrictamente economico:

- identificar los servicios AWS que generan costo;
- indicar como cobra AWS cada servicio;
- registrar precios de referencia oficiales cuando AWS los publica de forma explicita;
- dejar claro cuando el valor final depende de region, trafico, almacenamiento o capacidad.

## 2. Base de validacion

- Fecha de revision: **20 de mayo de 2026**
- Fuentes usadas: **paginas oficiales de pricing de AWS**
- Cuando un servicio depende de region, este documento usa como referencia:
  - valores oficiales publicados por AWS para **us-east-1** cuando la pagina los muestra de forma explicita; o
  - ejemplos oficiales de planes o de pricing publicados directamente por AWS.

## 3. Fuentes oficiales

- Amazon S3 Pricing: https://aws.amazon.com/s3/pricing/
- Amazon S3 pricing assumptions: https://aws.amazon.com/calculator/calculator-assumptions/
- Amazon CloudFront Pricing: https://aws.amazon.com/cloudfront/pricing/
- AWS Certificate Manager Pricing: https://aws.amazon.com/certificate-manager/pricing/
- AWS Fargate Pricing: https://aws.amazon.com/fargate/pricing/
- Amazon ECR Pricing: https://aws.amazon.com/ecr/pricing/
- Elastic Load Balancing Pricing: https://aws.amazon.com/elasticloadbalancing/pricing/
- Amazon RDS for MySQL Pricing: https://aws.amazon.com/rds/mysql/pricing/
- AWS Systems Manager Pricing: https://aws.amazon.com/systems-manager/pricing/
- AWS Secrets Manager Pricing: https://aws.amazon.com/secrets-manager/pricing/

## 4. Aclaracion obligatoria

AWS no publica un precio final unico para este sistema sin conocer:

- region de despliegue;
- trafico esperado;
- almacenamiento usado;
- tamano de base de datos;
- capacidad del backend;
- numero de solicitudes;
- tiempo real de ejecucion.

Por esa razon, este documento queda alineado con **precios AWS** del modo correcto:

- usa fuentes oficiales;
- registra precios de referencia monetarios cuando AWS los publica;
- y evita inventar una cotizacion cerrada cuando AWS cobra por consumo variable.

## 5. Tabla validada de precios AWS

| Servicio AWS | Como cobra AWS | Precio de referencia validado | Fuente oficial |
|---|---|---|---|
| Amazon S3 | Cobra por almacenamiento, solicitudes, transferencia y recuperacion cuando aplica | **S3 Standard:** `0.023 USD por GB/mes` para los primeros 50 TB al mes. **GET requests:** `0.0004 USD por 1,000 solicitudes` | https://aws.amazon.com/s3/pricing/ y https://aws.amazon.com/calculator/calculator-assumptions/ |
| Amazon CloudFront | Cobra por transferencia de datos y solicitudes en pay-as-you-go; tambien ofrece planes flat-rate oficiales | **CloudFront Free plan:** `0 USD/mes`, incluye `1 millon de solicitudes HTTP(S)` y `100 GB` de transferencia saliente. **CloudFront Pro plan:** `15 USD/mes`, incluye `10 millones de solicitudes HTTP(S)` y `50 TB` de transferencia saliente | https://aws.amazon.com/cloudfront/pricing/ |
| AWS Certificate Manager (ACM) | Cobra segun el tipo de certificado emitido | **Public certificates no exportables usados con servicios integrados de AWS:** `sin cargo adicional`. **Public certificates exportables:** `7 USD por FQDN/mes` y `79 USD por wildcard domain/mes`. **Llamadas API de exportacion adicionales:** `0.50 USD por cada 10,000 llamadas` despues del tramo gratuito indicado por AWS | https://aws.amazon.com/certificate-manager/pricing/ |
| AWS Fargate | Cobra por vCPU, memoria y almacenamiento adicional consumido por las tareas | Referencia oficial para **Linux/x86 en us-east-1**: `0.04048 USD por vCPU-hora` y `0.004445 USD por GB-hora`. El almacenamiento efimero incluido tiene un tramo base y el adicional se cobra aparte | https://aws.amazon.com/fargate/pricing/ |
| Amazon ECR | Cobra por almacenamiento de imagenes y transferencia a internet cuando aplica | **Private repository storage:** `0.10 USD por GB/mes`. **Transfer to AWS compute services in same region, including Fargate:** `0.00 USD por GB` | https://aws.amazon.com/ecr/pricing/ |
| Application Load Balancer (ALB) | Cobra por horas de balanceador y por capacidad consumida | Referencia oficial de ejemplo AWS: `0.025 USD por hora` de balanceador mas `0.008 USD por LCU-hora` en el ejemplo de pricing publicado por AWS | https://aws.amazon.com/elasticloadbalancing/pricing/ |
| Amazon RDS for MySQL | Cobra por tipo de instancia, almacenamiento, backups y extras de rendimiento | Referencia oficial gratuita publicada por AWS: `750 horas/mes` de `db.t3.micro` o `db.t4g.micro` Single-AZ y `20 GB` de almacenamiento `gp2` dentro del tramo gratuito indicado por AWS. Fuera de ese tramo, el precio depende de region, clase de instancia y almacenamiento | https://aws.amazon.com/rds/mysql/pricing/ |
| AWS Systems Manager Parameter Store | Cobra distinto para Standard y Advanced | **Standard parameters:** `sin cargo adicional`. **Advanced parameters:** `0.05 USD por parametro avanzado por mes`. **Advanced parameter interactions:** `0.05 USD por cada 10,000 interacciones API` | https://aws.amazon.com/systems-manager/pricing/ |
| AWS Secrets Manager | Cobra por secreto almacenado y por llamadas API | `0.40 USD por secreto por mes` y `0.05 USD por cada 10,000 llamadas API` | https://aws.amazon.com/secrets-manager/pricing/ |

## 6. Lectura economica por capa

### Frontend

Servicios principales:

- Amazon S3
- Amazon CloudFront
- AWS Certificate Manager (ACM)

Lectura economica:

- el frontend puede mantenerse en una banda de costo baja si el trafico inicial es bajo;
- `S3` cobra principalmente almacenamiento y solicitudes;
- `CloudFront` cobra trafico y solicitudes o puede operar bajo planes oficiales flat-rate;
- `ACM` puede no agregar costo si se usan certificados publicos no exportables integrados con servicios AWS.

### Backend

Servicios principales:

- AWS Fargate
- Amazon ECR
- Application Load Balancer (ALB)

Lectura economica:

- el backend tiende a ser una de las capas mas visibles en la factura;
- `Fargate` cobra computo real por vCPU y memoria;
- `ALB` agrega costo fijo por tiempo de uso y costo variable por capacidad;
- `ECR` suele ser menor que Fargate y ALB, pero no es cero.

### Base de datos y secretos

Servicios principales:

- Amazon RDS for MySQL
- AWS Systems Manager Parameter Store
- AWS Secrets Manager

Lectura economica:

- `RDS` suele representar un costo mensual estable y persistente;
- `Parameter Store Standard` puede costar `0 USD` adicionales;
- `Secrets Manager` tiene costo directo por secreto y por llamadas API;
- esta capa suele mantenerse en rango medio o medio-alto segun tamano y disponibilidad exigida.

## 7. Puntos que deben interpretarse correctamente

- `S3`, `CloudFront`, `Fargate`, `ALB` y `RDS` si tienen precios AWS, pero su factura final depende del consumo real.
- `ACM` no debe tratarse como un costo obligatorio en todos los casos; depende del tipo de certificado.
- `Parameter Store` y `Secrets Manager` no son equivalentes en precio.
- `RDS` no puede resumirse con una sola cifra universal sin definir region, instancia y almacenamiento.

## 8. Conclusiones de precios

Con base en pricing oficial de AWS:

- el frontend puede desplegarse con costo bajo usando `S3 + CloudFront`, especialmente en fases iniciales;
- el backend suele concentrar costo en `Fargate + ALB`;
- la base de datos introduce un costo continuo que no depende solo del trafico, sino de la capacidad reservada;
- la gestion de secretos puede ir desde `0 USD` adicionales en `Parameter Store Standard` hasta costos directos por secreto en `Secrets Manager`.

Este documento queda alineado a **precios AWS** porque:

- se apoya en fuentes oficiales;
- incluye precios monetarios de referencia;
- distingue claramente como cobra AWS cada servicio;
- y no inventa una cotizacion total donde AWS realmente cobra por uso variable.
