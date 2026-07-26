# 📋 Checklist de Aprendizaje y Reforzamiento - Backend & Cloud AWS

## 🔹 1. Desarrollo Backend & Bases de Datos (.NET Core & SQL)
- [ ] **C# y .NET moderno (.NET 8+)**
  - [ ] Patrones de diseño aplicados a APIs REST (Inyección de dependencias, Repository Pattern, CQRS).
  - [ ] Middleware nativo de .NET (Manejo global de excepciones con *Problem Details*, Rate Limiting).
  - [ ] Conceptos de diseño seguro de APIs: semántica de verbos HTTP, idempotencia en `PUT`/`DELETE` y manejo de reintentos seguros.
- [ ] **Bases de Datos Relacionales**
  - [ ] **SQL Server:** Optimización de consultas, stored procedures, índices y uso híbrido con Entity Framework Core / Dapper.
  - [ ] **PostgreSQL:** Particularidades del motor, tipos de datos específicos (JSONB) y configuración de cadenas de conexión seguras.

## ☁️ 2. Fundamentos y Servicios Core de AWS
- [ ] **Cómputo y Servidores**
  - [ ] **AWS EC2:** Creación de instancias, configuración de Security Groups, llaves SSH y manejo básico de redes (VPC, subredes públicas/privadas).
  - [ ] **AWS Lambda:** Desarrollo de funciones *serverless* con C#, triggers (S3, SQS, API Gateway), variables de entorno y ciclo de vida de la ejecución.
- [ ] **Almacenamiento y Datos**
  - [ ] **AWS S3:** Ciclo de vida de objetos (*Lifecycle policies*), políticas de buckets, versionamiento y seguridad de acceso.
  - [ ] **AWS RDS:** Despliegue de instancias administradas (PostgreSQL/SQL Server), backups automáticos, multi-AZ para alta disponibilidad y escalado.
- [ ] **Integración de Datos / Serverless ETL**
  - [ ] **AWS Glue:** Conceptos clave de ETL (Extract, Transform, Load), uso de Data Catalogs, Crawlers y ejecución de Jobs para mover volúmenes de datos hacia el stack analítico o relacional.

## 🛠️ 3. Infraestructura como Código (IaC)
- [ ] **Terraform (Enfoque Declarativo)**
  - [ ] Sintaxis HCL básica para provisionar recursos (EC2, S3, RDS).
  - [ ] Ciclo de vida de Terraform (`terraform init`, `plan`, `apply`, `destroy`).
  - [ ] Gestión del estado: uso de backends remotos (guardar el `.tfstate` de manera segura en un bucket de S3 con bloqueo en DynamoDB).
- [ ] **AWS CDK en C# (Enfoque Imperativo)**
  - [ ] Concepto de síntesis (`cdk synth`): cómo compilar código C# y traducirlo a plantillas nativas de CloudFormation.
  - [ ] Flujo de despliegue (`cdk diff`, `cdk deploy`) delegando el control del estado 100% a AWS CloudFormation.
  - [ ] Estrategias de importación de recursos existentes:
    - [ ] Referenciar con métodos de solo lectura (`Bucket.FromBucketName`).
    - [ ] Tomar control total del ciclo de vida usando el comando guiado `cdk import`.

## 📦 4. Contenedores y Cultura DevOps (CI/CD)
- [ ] **Dockerización**
  - [ ] Creación de `Dockerfile` optimizados para microservicios y APIs en .NET Core.
  - [ ] Uso de `docker-compose` para montar laboratorios locales multicontenedor (API + base de datos local en PostgreSQL/SQL Server).
- [ ] **Orquestación (Kubernetes)**
  - [ ] Conceptos fundamentales: Pods, Deployments, Services e Ingress Controllers.
  - [ ] Entendimiento conceptual del rol de K8s en producción (escalabilidad automática, balanceo de carga, autoreparación de pods).
- [ ] **Automatización y CI/CD**
  - [ ] **GitHub & Git:** Gestión de repositorios corporativos, protección de ramas (*branch protection*), estrategias de branching (GitFlow / Trunk-based development).
  - [ ] **GitHub Actions / Azure Pipelines:** Estructuración de pipelines para compilar código, ejecutar pruebas unitarias (`xUnit`/`Moq`), empaquetar imágenes de Docker, subirlas a repositorios como Amazon ECR y ejecutar despliegues automáticos mediante Terraform o CDK.