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

## 5.- ☸️ Repositorio: `gitops-manifests` (Argo CD / GitOps)
**Propósito:** Gestionar el estado deseado del clúster de Kubernetes siguiendo el modelo GitOps.

* **Estructura:**
    ```text
    /apps/
      /node-app/
        /overlays/
          /dev/       -> Configuración específica para desarrollo
          /prod/      -> Configuración con réplicas y recursos de producción
        /base/        -> Manifiestos base (Kustomize/Helm)
    /clusters/
      /aws-eks-01/    -> Configuración del Application Controller de Argo
    ```
* **Prácticas clave:**
    * **Self-healing:** Probar cómo Argo CD revierte cambios manuales hechos con `kubectl`.
    * **Sync Strategies:** Configurar despliegues automáticos (Pruning/Recurse).
    * **App-of-Apps Pattern:** Usar un manifiesto de Argo para desplegar múltiples aplicaciones a la vez.

---

## 💡 Notas de Implementación
> 1. Todos los repositorios serán **Públicos** en la Organización para habilitar las funciones de Environments y Rulesets sin costo.
> 2. Se utilizará un **"Segundo Desarrollador"** (cuenta secundaria) para validar Pull Requests y probar las Branch Protection Rules.
> 3. El flujo de trabajo estándar será: `Fork/Branch` -> `Pull Request` -> `Status Checks (Actions)` -> `Review` -> `Merge`.