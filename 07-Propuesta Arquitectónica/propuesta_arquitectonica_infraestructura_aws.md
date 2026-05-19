# Propuesta Arquitectónica de Infraestructura (Servicios de AWS)

## 1. Introducción

El presente documento describe una propuesta arquitectónica de referencia para el despliegue del Sistema Veterinario sobre AWS. Su propósito es orientar técnicamente la futura implementación en la nube sin asumir que todas las decisiones de infraestructura ya se encuentran cerradas.

La propuesta se formula sobre una arquitectura web de tres capas:

- capa de presentación: frontend web;
- capa de servicios: backend o API;
- capa de datos: persistencia relacional.

Esta sección debe entenderse como una arquitectura recomendada y evolutiva. Algunos componentes pueden adoptarse desde una primera etapa de despliegue y otros pueden incorporarse posteriormente según alcance, presupuesto, complejidad operativa y madurez del proyecto.

---

## 2. Objetivo de la infraestructura

La infraestructura propuesta busca ofrecer una base técnica segura, escalable y mantenible para publicar el sistema en AWS.

### Problemas que pretende resolver

- publicar la aplicación de manera controlada;
- separar frontend, backend y base de datos;
- proteger la información operativa y clínica;
- permitir crecimiento futuro sin rediseñar completamente la solución;
- facilitar monitoreo, soporte y administración;
- restringir accesos por perfil y por ambiente cuando aplique.

### Beneficios esperados

- mejor organización de los componentes del sistema;
- mayor seguridad lógica y de red;
- posibilidad de escalar por etapas;
- mayor trazabilidad operativa;
- mejor capacidad de mantenimiento y evolución.

---

## 3. Alcance de la propuesta

Esta propuesta **no obliga** a implementar desde el primer despliegue todos los servicios aquí mencionados. Se recomienda distinguir entre:

### Arquitectura mínima viable

Corresponde al conjunto mínimo necesario para publicar la aplicación en AWS de forma funcional y ordenada.

Puede incluir:

- hosting del frontend;
- despliegue del backend;
- base de datos;
- red básica;
- seguridad básica;
- gestión mínima de accesos;
- monitoreo y logs esenciales.

### Arquitectura objetivo

Corresponde a la evolución deseable de la solución cuando el proyecto requiera mayor robustez, seguridad y capacidad operativa.

Puede incluir:

- alta disponibilidad;
- balanceo de carga;
- despliegue en múltiples zonas;
- control más fino de acceso;
- monitoreo ampliado;
- auditoría de configuración;
- endurecimiento de seguridad;
- optimización de costos avanzada.

---

## 4. Arquitectura general

## Vista lógica de referencia

```text
Usuarios internos
   |
   v
DNS / Acceso web
   |
   v
Frontend
   |
   v
Backend / API
   |
   v
Base de datos
   |
   v
Monitoreo, seguridad y auditoría
```

## Componentes generales

### Frontend
Aplicación web consumida por usuarios internos desde navegador.

### Backend o API
Componente encargado de la lógica del negocio, autenticación, validaciones y acceso a datos.

### Base de datos
Componente relacional para almacenar clientes, mascotas, citas, historias clínicas, tratamientos y demás información del sistema.

### Seguridad
Controles de identidad, red, cifrado y gestión de secretos.

### Monitoreo
Métricas, logs y trazabilidad de cambios sobre el entorno.

---

## 5. Servicios AWS utilizados

Los siguientes servicios se presentan como opciones recomendadas. Su adopción final depende de cómo se defina el despliegue real del proyecto.

## Amazon S3

### Qué es
Servicio de almacenamiento de objetos.

### Para qué se puede usar
Hospedar el frontend del sistema si este se publica como sitio estático.

### Por qué se considera
Es una opción simple, económica y común para aplicaciones web desacopladas.

### Beneficios técnicos

- alta durabilidad;
- bajo costo operativo;
- despliegue sencillo;
- integración con CloudFront.

## Amazon CloudFront

### Qué es
Servicio de distribución de contenido.

### Para qué se puede usar
Exponer el frontend con mejor rendimiento y HTTPS.

### Por qué se considera
Es útil cuando el frontend se publica como contenido estático o cuando se desea mejorar desempeño y seguridad en el acceso.

### Beneficios técnicos

- baja latencia;
- caché distribuida;
- integración con TLS;
- protección del origen.

## Amazon Route 53

### Qué es
Servicio DNS administrado.

### Para qué se puede usar
Asociar dominios o subdominios al frontend y backend del sistema.

### Por qué se considera
Permite una administración centralizada del acceso público.

### Beneficios técnicos

- alta disponibilidad;
- administración de zonas DNS;
- integración con otros servicios AWS.

## AWS Certificate Manager (ACM)

### Qué es
Servicio de certificados SSL/TLS administrados.

### Para qué se puede usar
Habilitar HTTPS para frontend y backend.

### Por qué se considera
Permite proteger las comunicaciones sin administrar manualmente certificados.

### Beneficios técnicos

- renovación automática;
- integración con CloudFront y balanceadores;
- mejora de seguridad de acceso.

## Amazon VPC

### Qué es
Red virtual privada en AWS.

### Para qué se puede usar
Definir el entorno de red del sistema y segmentar recursos.

### Por qué se considera
Toda solución seria en AWS debe operar dentro de una red controlada.

### Beneficios técnicos

- aislamiento lógico;
- segmentación por subredes;
- control del tráfico de red;
- integración con Security Groups y NACL.

## Amazon EC2

### Qué es
Servicio de máquinas virtuales.

### Para qué se puede usar
Desplegar el backend de forma directa o con contenedores administrados manualmente.

### Por qué se considera
Es una alternativa válida cuando se busca simplicidad inicial o mayor control del entorno.

### Beneficios técnicos

- flexibilidad de configuración;
- control total del sistema operativo;
- útil para despliegues iniciales o académicos.

## Amazon ECS con AWS Fargate

### Qué es
Servicio administrado para ejecución de contenedores.

### Para qué se puede usar
Desplegar el backend si el proyecto adopta contenedores como estrategia de ejecución.

### Por qué se considera
Es una alternativa moderna con menor carga operativa que administrar servidores.

### Beneficios técnicos

- despliegue consistente;
- integración con ECR;
- escalado más sencillo;
- menor administración de infraestructura base.

### Nota de adopción
Solo debe proponerse como implementación real si el equipo confirma que el backend se desplegará en contenedores.

## Amazon ECR

### Qué es
Repositorio de imágenes de contenedores.

### Para qué se puede usar
Almacenar imágenes Docker del backend.

### Por qué se considera
Complementa una estrategia basada en contenedores.

### Beneficios técnicos

- versionamiento de imágenes;
- integración con ECS;
- control por IAM.

## Application Load Balancer (ALB)

### Qué es
Balanceador de carga de capa 7.

### Para qué se puede usar
Distribuir tráfico hacia varias instancias o tareas del backend.

### Por qué se considera
Es útil cuando se requiere alta disponibilidad o múltiples réplicas del backend.

### Beneficios técnicos

- health checks;
- terminación TLS;
- distribución de tráfico;
- soporte a escalabilidad horizontal.

## Amazon RDS for MySQL

### Qué es
Servicio administrado de base de datos relacional.

### Para qué se puede usar
Persistir los datos del sistema usando MySQL.

### Por qué se considera
El dominio del proyecto es relacional y el stack es compatible con MySQL.

### Beneficios técnicos

- backups automáticos;
- administración simplificada;
- monitoreo integrado;
- posibilidad de evolucionar a alta disponibilidad.

## AWS IAM

### Qué es
Servicio de identidad y acceso.

### Para qué se usa
Administrar usuarios, roles, grupos y permisos sobre la infraestructura.

### Por qué se considera
Es el componente base para el control de acceso en AWS.

### Beneficios técnicos

- mínimo privilegio;
- trazabilidad de accesos;
- separación de responsabilidades;
- control granular de permisos.

## AWS Secrets Manager

### Qué es
Servicio para gestionar secretos.

### Para qué se puede usar
Almacenar credenciales de base de datos, claves y secretos del backend.

### Por qué se considera
Evita exponer credenciales dentro del código o archivos inseguros.

### Beneficios técnicos

- cifrado;
- control de acceso;
- mejor manejo de secretos.

## Amazon CloudWatch

### Qué es
Servicio de monitoreo, métricas y logs.

### Para qué se usa
Monitorear el estado operativo del sistema.

### Por qué se considera
Permite centralizar métricas y eventos del entorno.

### Beneficios técnicos

- métricas;
- logs;
- alarmas;
- paneles de observabilidad.

## AWS CloudTrail

### Qué es
Servicio de auditoría de acciones sobre la cuenta AWS.

### Para qué se usa
Registrar cambios administrativos y eventos relevantes sobre la infraestructura.

### Por qué se considera
Es fundamental para auditoría y trazabilidad.

### Beneficios técnicos

- historial de acciones;
- apoyo a investigación de incidentes;
- visibilidad de cambios.

## AWS Config

### Qué es
Servicio de evaluación de configuración.

### Para qué se puede usar
Supervisar cambios en recursos y detectar desviaciones.

### Por qué se considera
Es útil en etapas de mayor madurez de seguridad y gobierno.

### Beneficios técnicos

- historial de configuración;
- validación de cumplimiento;
- mayor control del entorno.

## AWS WAF

### Qué es
Firewall de aplicaciones web.

### Para qué se puede usar
Proteger aplicaciones expuestas públicamente frente a amenazas comunes.

### Por qué se considera
Es una mejora de seguridad útil cuando el sistema esté publicado y requiera mayor protección.

### Beneficios técnicos

- filtrado de tráfico malicioso;
- endurecimiento del acceso público;
- integración con CloudFront y ALB.

### Nota de adopción
Se recomienda como mejora futura o de endurecimiento, no como requisito mínimo obligatorio.

## Amazon API Gateway

### Qué es
Servicio administrado para exposición de APIs.

### Para qué se puede usar
Publicar APIs en escenarios serverless o de microservicios.

### Por qué se considera
Puede ser útil si el proyecto evoluciona a una arquitectura con mayor desacoplamiento.

### Beneficios técnicos

- control de exposición de API;
- integración con autenticación y observabilidad;
- soporte para arquitectura serverless.

### Nota de adopción
No es obligatorio si el backend se expone directamente por EC2, ECS o balanceador.

---

## 6. Seguridad

La seguridad de la infraestructura debe definirse independientemente del mecanismo exacto de despliegue.

## IAM Roles
Se recomienda separar roles para:

- ejecución del backend;
- despliegue;
- administración;
- lectura y observación;
- acceso académico de solo lectura.

## Policies
Se recomienda aplicar políticas con:

- mínimo privilegio;
- permisos acotados por servicio y recurso;
- separación entre lectura, operación y administración.

## MFA
Debe exigirse para:

- usuarios administrativos;
- accesos humanos a consola;
- cuentas con privilegios elevados.

## Security Groups
Definir grupos de seguridad para restringir el tráfico entre:

- acceso público;
- backend;
- base de datos.

## NACL
Pueden usarse como capa adicional si la complejidad del entorno lo requiere.

## Cifrado
Aplicar cifrado:

- en tránsito mediante HTTPS/TLS;
- en reposo sobre almacenamiento, base de datos y secretos.

## Least Privilege

- no operar con root;
- no compartir credenciales;
- usar roles separados;
- evitar permisos excesivos o wildcard innecesarios.

---

## 7. Escalabilidad y disponibilidad

La escalabilidad y disponibilidad deben adaptarse a la forma real en que el sistema se despliegue.

## Si se despliega sobre EC2

- se puede usar Auto Scaling Group;
- se puede incorporar balanceador de carga si existen múltiples instancias.

## Si se despliega sobre ECS/Fargate

- se puede aplicar Service Auto Scaling;
- se puede integrar con balanceador de carga y múltiples tareas.

## Alta disponibilidad
Como arquitectura objetivo se recomienda:

- distribución del frontend;
- múltiples zonas de disponibilidad;
- backend replicado si la carga lo requiere;
- base de datos con tolerancia a fallos en ambientes críticos.

## Tolerancia a fallos

- health checks;
- reinicio o reprovisión de componentes;
- backups automáticos;
- monitoreo de fallos.

---

## 8. Monitoreo y logs

## CloudWatch
Se recomienda usarlo para:

- métricas del backend;
- métricas del frontend cuando apliquen;
- alarmas;
- logs centralizados.

## CloudTrail
Se recomienda usarlo para:

- auditar cambios administrativos;
- revisar eventos sobre la cuenta AWS;
- apoyar trazabilidad.

## AWS Config
Se recomienda como capa adicional para:

- detectar configuraciones inseguras;
- registrar cambios de configuración;
- fortalecer gobierno y cumplimiento.

## Consideración práctica
El nivel real de monitoreo puede comenzar con logs y métricas básicas, y ampliarse después hacia auditoría y validación de configuración.

---

## 9. Costos y optimización

La optimización de costos debe responder al tamaño real del despliegue.

## Estrategias recomendadas

### Pago por uso
Adecuado para entornos académicos y primeras etapas.

### Uso progresivo de servicios administrados
Permite crecer sin asumir complejidad operativa excesiva desde el inicio.

### Frontend estático cuando aplique
Puede reducir costos frente a soluciones más complejas.

### Ajuste por ambiente

- ambientes no productivos con menor capacidad;
- producción con dimensionamiento según demanda real.

### Políticas de ciclo de vida
Aplicables a:

- logs;
- respaldos;
- artefactos obsoletos.

### Ahorro en cargas estables
Si la solución evoluciona a uso continuo, pueden evaluarse:

- Savings Plans;
- instancias reservadas;
- ajuste fino de recursos.

---

## 10. Buenas prácticas AWS Well-Architected

## Seguridad

- MFA;
- mínimo privilegio;
- gestión segura de secretos;
- cifrado;
- segmentación de red;
- auditoría.

## Fiabilidad

- backups;
- monitoreo;
- recuperación ante fallos;
- despliegues controlados.

## Rendimiento

- elección adecuada del modelo de despliegue;
- distribución del frontend cuando aplique;
- escalado según carga;
- elección correcta del motor de base de datos.

## Optimización de costos

- evitar sobreaprovisionamiento;
- dimensionar por ambiente;
- priorizar servicios acordes al alcance real;
- evolucionar la arquitectura según necesidad.

## Excelencia operativa

- despliegues repetibles;
- observabilidad;
- control de cambios;
- documentación clara;
- mejora continua.

---

## 11. Conclusión

La infraestructura propuesta para el Sistema Veterinario debe entenderse como una guía técnica de referencia y no como una implementación cerrada desde el inicio. Su valor principal está en definir una ruta arquitectónica coherente para desplegar el sistema en AWS con criterios de seguridad, escalabilidad, observabilidad y orden operativo.

En términos profesionales, esta propuesta permite:

- justificar técnicamente servicios AWS adecuados;
- adaptar la implementación al alcance real del proyecto;
- crecer por etapas sin perder coherencia arquitectónica;
- distinguir entre una arquitectura mínima viable y una arquitectura objetivo.

De esta forma, el documento funciona como una base sólida para la toma de decisiones futuras sobre el despliegue del sistema en AWS, sin comprometer al proyecto con componentes que todavía no han sido definidos de manera definitiva.
