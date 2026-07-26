# 📋 AWS IAM Study Checklist: Developer (DVA-C02) & CloudOps (SOA-C03)

## 🔀 Core Overlap (Required for Both Exams)
- [ ] **IAM Fundamentals**: Users, Groups, Roles, and Policies.
- [ ] **Policy Document Structure**: Understanding the JSON elements: `Effect`, `Action`, `Resource`, and `Condition`.
- [ ] **Principle of Least Privilege**: Designing policies that grant only the minimum necessary access.
- [ ] **IAM Roles for Services**: How AWS services (like EC2 instances and ECS tasks) securely assume roles to interact with other resources.
- [ ] **AWS STS (Security Token Service)**: Generating and using temporary, limited-privilege credentials.
- [ ] **MFA (Multi-Factor Authentication)**: Enforcing and verifying MFA requirements in IAM policies.

## 💻 Developer Focus (DVA-C02)
- [ ] **Programmatic Access**: Managing and securing Access Keys and Secret Keys for the AWS CLI and SDKs.
- [ ] **Assuming Roles via Code**: Using STS `AssumeRole` within your application code to interact with cross-account resources.
- [ ] **Lambda Execution Roles**: Distinguishing between Lambda execution roles (what the function can do) and resource-based policies (who can invoke the function).
- [ ] **API Gateway Authorization**: Using IAM signatures (SigV4) to secure API Gateway endpoints.
- [ ] **Resource-Based Policies**: Writing and evaluating resource policies for Amazon S3, SQS, and KMS.
- [ ] **Web Identity Federation & Cognito**: Mapping Amazon Cognito Identity Pools to IAM roles for mobile/web app users.
- [ ] **The `iam:PassRole` Permission**: Understanding when and why a developer needs this permission to assign a role to an AWS service (e.g., passing a role to an EC2 instance or ECS task).

## 🛠️ CloudOps / SysOps Focus (SOA-C03)
- [ ] **Cross-Account Access**: Configuring trust policies and IAM roles to allow users from one AWS account to access resources in another.
- [ ] **AWS Organizations & SCPs**: Using Service Control Policies (SCPs) to enforce hard guardrails across entire AWS Accounts or Organizational Units (OUs).
- [ ] **AWS IAM Identity Center (formerly AWS SSO)**: Managing workforce access centrally and integrating with external identity providers (Active Directory, SAML 2.0).
- [ ] **Permissions Boundaries**: Using managed policies to set the maximum permissions that an IAM administrator can grant to IAM users or roles.
- [ ] **Auditing & Troubleshooting**: 
  - [ ] **IAM Policy Simulator**: Testing and troubleshooting complex policy interactions.
  - [ ] **IAM Access Analyzer**: Identifying resources shared with external entities.
  - [ ] **AWS CloudTrail**: Auditing IAM API calls to see who assumed a role or modified a policy.
- [ ] **Credential Lifecycle Management**: Enforcing password policies and access key rotation schedules.

## 🔐 1. AWS IAM (Identity and Access Management)

### Fundamentos Clave y Entidades
- [ ] **Usuarios de IAM (IAM Users):** Entender cuándo usarlos (servicios legacy/acceso físico de emergencia) y por qué las buenas prácticas actuales exigen evitarlos a favor de AWS IAM Identity Center.
- [ ] **Grupos de IAM (IAM Groups):** Gestión de permisos masivos a nivel de usuarios.
- [ ] **Roles de IAM (IAM Roles):** Concepto de asumir identidades temporales. Diferencia entre roles de servicio (ej: rol para que una EC2/Lambda actúe) y roles de asunción cruzada (*Cross-Account Roles*).
- [ ] **Políticas de IAM (IAM Policies):** - [ ] Diferencia entre políticas administradas por AWS (*AWS Managed*), administradas por el cliente (*Customer Managed*) y políticas en línea (*Inline Policies*).
  - [ ] Estructura exacta de un documento JSON de política (`Version`, `Statement`, `Effect`, `Principal`, `Action`, `Resource`, `Condition`).

### Conceptos Avanzados y Mecanismos de Evaluación
- [ ] **Políticas Basadas en Identidad vs. Políticas Basadas en Recursos:** Entender cómo interactúan los permisos cuando se configuran en el usuario/rol frente a cuando se configuran directamente en el recurso (ej: Bucket Policies).
- [ ] **Evaluación lógica de IAM:** Cómo AWS determina un *Allow* o un *Deny* (El principio del *Explicit Deny* por sobre cualquier *Allow*).
- [ ] **Límites de Permisos (Permissions Boundaries):** Cómo restringir el alcance máximo de privilegios que un usuario o rol puede otorgarse a sí mismo o a otros.
- [ ] **Políticas de Control de Servicios (SCPs - Service Control Policies):** Cómo se usan a nivel de AWS Organizations para restringir acciones de forma global en cuentas enteras (vital para SysOps).

### Práctica y Casos de Entrevista (SysOps / Developer)
- [ ] **Principio de Mínimo Privilegio (*Least Privilege*):** Cómo auditar y refinar políticas excesivamente permisivas (reemplazar `*` por recursos y acciones específicas).
- [ ] **Credenciales Temporales:** Cómo funciona el servicio STS (*Security Token Service*) y la API `AssumeRole`.
- [ ] **Uso de IAM en Código:** Evitar "hardcodear" *Access Keys* y *Secret Keys*. Uso correcto de perfiles locales o roles de ejecución vinculados a contenedores/servicios.