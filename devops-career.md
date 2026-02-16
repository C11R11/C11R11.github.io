# Mapa Conceptual: La Explosión de la Demanda DevOps en 2026

## 1. El Contexto del Mercado (La "Ventana de Oportunidad")
* **Ciclo Histórico:** DevOps hoy = Ingeniería de Software hace 10 años.
    * *Pasado:* Alta demanda/baja oferta permitió entrar con bases sencillas.
    * *Presente:* DevOps tiene un gap de habilidades del 37%. Las vacantes tardan 3x más en llenarse que otros roles IT.
* **Urgencia:** La oferta eventualmente alcanzará a la demanda (en 2-10 años), elevando la barra de entrada.

## 2. Los 5 Factores Críticos de la Demanda
### A. Aceleración de la Nube
* Migración masiva de sistemas legacy a la nube.
* Necesidad de modernización constante.
### B. Estandarización de Kubernetes
* Los contenedores ya no son "tecnología punta", son la norma.
* Demanda de gestión de aplicaciones orquestadas a escala.
### C. Seguridad "Shift Left" (DevSecOps)
* Brechas de datos semanales y nuevas regulaciones.
* La seguridad ya no es un paso final, debe integrarse en cada etapa del pipeline.
### D. Infraestructura como Código (IaC)
* Desaparición de la configuración manual.
* Necesidad de versionamiento, testeo y despliegue automático de infra (Terraform).
### E. El Factor IA (Impacto Doble)
* **Revalorización del rol:** La IA automatiza el código básico (Junior), pero no la toma de decisiones arquitectónicas o diseño de sistemas complejos.
* **MLOps:** Los proyectos de IA necesitan pipelines de datos, versionamiento de modelos y reentrenamiento continuo (80% habilidades DevOps).

## 3. El Perfil del Ingeniero DevOps Exitoso
* **Rol de Arquitecto:** No es solo escribir código, es diseñar el sistema completo.
* **Puente de Comunicación:** Capacidad de hablar el lenguaje de Desarrollo y de Operaciones.
* **Mentalidad de Automatización:** Eliminar cuellos de botella en todo el ciclo de vida del software.

## 4. RoadMap de Aprendizaje Estructurado (El "Edificio")
*Para aprender DevOps no se puede empezar por el 5to piso; se necesita una base sólida:*
1. **Cimientos:** Linux y Redes.
2. **Piso 1:** Git y Bash Scripting.
3. **Piso 2:** Docker (Contenedores).
4. **Piso 3 (El Corazón):** CI/CD (GitHub Actions, Jenkins o GitLab).
5. **Piso 4:** Una plataforma Cloud (AWS es la recomendada por mercado).
6. **Piso 5:** Kubernetes.
7. **Piso 6:** Infraestructura como Código (Terraform).
8. **Techo:** Monitoreo y Troubleshooting (Prometheus, Grafana).

## 5. Errores Críticos a Evitar
* **Aprendizaje Aislado:** Aprender herramientas por separado sin entender cómo se conectan en un proceso end-to-end.
* **El "Infierno de los Tutoriales":** Copiar código de entornos controlados (sandboxes) sin construir proyectos desde cero.
* **Costo de Oportunidad:** Perder años intentando aprender gratis sin estructura vs. invertir en educación para ganar salarios altos mucho antes.
* **Aprender en Silencio:** No documentar el progreso en LinkedIn o GitHub. La visibilidad atrae a los reclutadores sin necesidad de buscarlos.

---
*Resumen del video: "Why DevOps Demand Is Exploding (5 Critical Factors)" - TechWorld with Nana (2026)*


# Plan general platform engineer

Plan para perfección en platform engineering y desarrollo.

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

* [ AWS Lambda](aws-lambda.md) - Aws lambda
* [Aws developer](aws-cert) - Plan estudio certificación aws developer

---

## 📍 Fase 3: Especialización y Diferenciación (Semanas 12+)
### Opción A: Enfoque en Contenedores (Kubernetes)
- [ ] **CKAD (Certified Kubernetes Application Developer):** Ideal para ti porque se enfoca en el uso de K8s desde la perspectiva del desarrollador.

### Opción B: Enfoque en DevSecOps
- [ ] **GitHub Actions Certification:** Certificación oficial de GitHub para automatización de workflows y seguridad (SAST/SCA).

* [Github cert](github-cert.md) Guia certificación github (GH-200)
* [Github](github.md) - Github

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


# 🗺️ Roadmap de Certificaciones: Arquitectura de Laboratorios

Este documento define la estructura de la Organización y los Repositorios diseñados para cubrir los objetivos de las certificaciones **GH-200 (GitHub)**, **Terraform Associate** y **AWS Developer**.

---

## 🏢 1. Organización: `cert-labs-hq` (Gobernanza)
**Propósito:** Simular un entorno Enterprise para practicar políticas de seguridad y control de acceso.

* **Owner:** Tu cuenta principal.
* **Teams:**
    * `platform-admins`: Control total de IaC y Workflows.
    * `developers`: Acceso limitado a repos de aplicaciones.
* **Configuración clave:**
    * **Organization Secrets:** Almacenar el `AWS_ROLE_ARN` para OIDC.
    * **Settings:** Restringir la creación de repositorios y forzar MFA.

---

## 📂 2. Repositorio: `dev-ops` (The Golden Path)
**Propósito:** Repositorio central de herramientas y automatización reutilizable.

* **Contenido:**
    * `.github/workflows/`: Reusable Workflows (CI para Node, Deploy de Terraform).
    * `.github/actions/`: Custom Composite Actions para tareas repetitivas (ej: setup de entorno).
* **Prácticas GH-200:**
    * Versionado de acciones vía Tags (`v1.0.0`).
    * Uso de `workflow_call` y herencia de secretos.

---

## 🏗️ 3. Repositorio: `infrastructure` (IaC & Cloud)
**Propósito:** Gestión de recursos en AWS mediante Terraform.

* **Estructura Sugerida:**
```text
/modules        -> Lógica de recursos (VPC, S3, RDS).
/environments   -> Implementación (dev/prod) con Remote State.
```
* **Prácticas Terraform/AWS:**
    * **State Management:** S3 + DynamoDB para Locking.
    * **Identidad:** Conexión vía OIDC (sin AWS Keys manuales).
    * **Seguridad:** Encriptación de Buckets y políticas de IAM mínimas.

---

## 🚀 4. Repositorios de Aplicación (App Use Cases)
**Propósito:** Repositorios ligeros que consumen la infraestructura y los flujos DevOps.

* **Ejemplos:** `app-node-express`, `app-python-api`.
* **Interacción:**
    * El código vive aquí, pero el **CI/CD** es una llamada al repo `dev-ops`.
    * El **Despliegue** se gatilla hacia los recursos creados por el repo `infrastructure`.
* **Prácticas Clave:**
    * **Environments:** Uso de aprobación manual para despliegue a `Production`.
    * **Dependabot:** Escaneo automático de vulnerabilidades.

---

## 💡 Notas de Implementación
> 1. Todos los repositorios serán **Públicos** en la Organización para habilitar las funciones de Environments y Rulesets sin costo.
> 2. Se utilizará un **"Segundo Desarrollador"** (cuenta secundaria) para validar Pull Requests y probar las Branch Protection Rules.
> 3. El flujo de trabajo estándar será: `Fork/Branch` -> `Pull Request` -> `Status Checks (Actions)` -> `Review` -> `Merge`.