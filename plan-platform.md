# 🗺️ Roadmap de Certificaciones: Platform Engineer & SDLC Specialist

**Estrategia:** Priorizar la automatización y herramientas de plataforma para destacar como un "DevOps que entiende al Desarrollador".

---

## 📍 Fase 1: La Victoria Rápida (Semanas 1-3)
### Certificación: **HashiCorp Certified: Terraform Associate**
*Por qué: Es el estándar de oro para Infrastructure as Code (IaC) y es la certificación más rápida de obtener con tu experiencia previa.*

- [ ] **Fundamentos de IaC:** Beneficios de Terraform vs scripts manuales.
- [ ] **Terraform Basics:** Providers, Resources, Data Sources y Variables.
- [ ] **State Management:** Backend en S3, State Locking con DynamoDB (Crucial para equipos).
- [ ] **Módulos:** Creación de infraestructura reutilizable (La base del "Golden Path").
- [ ] **Workflow:** Init, Plan, Apply, Destroy.
- [ ] **Terraform Cloud & Enterprise:** Diferencias básicas y Sentinel (Policy as Code).

https://developer.hashicorp.com/terraform/tutorials/certification-004

---

## 📍 Fase 2: Consolidación Cloud (Semanas 4-8)
### Certificación: **AWS Certified Developer Associate (DVA-C02)**
*Por qué: Valida tu capacidad para construir y desplegar aplicaciones. Es más relevante para el SDLC que la de Arquitectura.*

- [ ] **Serverless:** AWS Lambda, API Gateway y AWS Amplify.
- [ ] **Bases de Datos:** DynamoDB (Índices GSI/LSI, RCU/WCU).
- [ ] **CI/CD de AWS:** CodePipeline, CodeBuild, CodeDeploy.
- [ ] **Despliegues:** Blue/Green, Canary y AppSpec/BuildSpec files.
- [ ] **Seguridad:** KMS, Secrets Manager y Roles de IAM para aplicaciones.
- [ ] **Observabilidad:** AWS X-Ray y CloudWatch Logs/Metrics.

---

## 📍 Fase 3: Especialización y Diferenciación (Semanas 12+)
### Opción A: Enfoque en Contenedores (Kubernetes)
- [ ] **CKAD (Certified Kubernetes Application Developer):** Ideal para ti porque se enfoca en el uso de K8s desde la perspectiva del desarrollador.

### Opción B: Enfoque en DevSecOps
- [ ] **GitHub Actions Certification:** Certificación oficial de GitHub para automatización de workflows y seguridad (SAST/SCA).

> Preparación Github actions
* https://learn.microsoft.com/en-us/credentials/certifications/github-actions/?practice-assessment-type=certification
> Preparación Github secutiry
* https://learn.microsoft.com/en-us/credentials/certifications/github-advanced-security/?practice-assessment-type=certification

### Recursos

> Esta guia permite crear una imagen desde un build dotnet, subir la imágen a un contenedor, y desplegar en un cluster azure <br>
* [Deploy a cloud-native .NET microservice automatically with GitHub Actions and Azure Pipelines](https://learn.microsoft.com/en-us/training/modules/microservices-devops-aspnet-core/)


---

## 📈 Impacto en el Perfil de LinkedIn / CV

| Certificación | Palabras Clave para el Algoritmo | Valor para el Negocio |
| :--- | :--- | :--- |
| **Terraform Associate** | IaC, GitOps, Automation, Multi-cloud | "Automatizo la creación de ambientes, eliminando errores manuales." |
| **AWS Developer** | Serverless, SDLC, Microservices, CI/CD | "Diseño el camino más rápido y seguro desde el código a producción." |
| **GitHub Actions** | DevSecOps, Workflow Automation, DX | "Integro seguridad y calidad de forma invisible para el desarrollador." |

---

## 💡 Consejos de Aplicación en Chile (2026)

1. **El "Combo" Ganador:** En Chile, las empresas que usan AWS casi siempre usan Terraform. Tener ambas certificaciones te pone en el 5% superior de los postulantes.
2. **Postulación Activa:** No esperes a tener la de AWS. Apenas obtengas la de **Terraform**, actualiza tu perfil. Esa certificación sola ya te abrirá entrevistas para cargos de **Platform Engineer**.
3. **Proyectos Reales:** Para cada certificación, intenta tener un repo en GitHub que diga: "Infraestructura de ECS desplegada con Terraform" o "Pipeline de CI/CD con seguridad integrada".