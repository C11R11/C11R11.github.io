

# AWS Certified Developer - Associate (DVA-C02) Checklist

* [AWS Certified Developer - Associate (DVA-C02) Exam Guide](https://d1.awsstatic.com/training-and-certification/docs-dev-associate/AWS-Certified-Developer-Associate_Exam-Guide.pdf)
* [ AWS Cli](aws-cli.md) - Aws cli 
* [ AWS Lambda](aws-lambda.md) - Aws lambda

## 🔴 Domain 1: Development with AWS Services (32%)
- [ ] **AWS Lambda**
  - [ ] Execution context, concurrency limits, and cold starts
  - [ ] Synchronous vs. Asynchronous vs. Event Source Mapping invocations
  - [ ] Deployment packages (.zip vs. Container images) and Environment Variables
- [ ] **Amazon DynamoDB**
  - [ ] Partition Keys vs. Composite Keys
  - [ ] Calculating WCU and RCU (Strongly Consistent vs. Eventually Consistent vs. Transactional)
  - [ ] Local Secondary Indexes (LSI) vs. Global Secondary Indexes (GSI)
  - [ ] DynamoDB Streams & TTL (Time to Live)
- [ ] **Amazon API Gateway**
  - [ ] Lambda Proxy Integration vs. Custom Integration
  - [ ] Deployment Stages, Canary deployments, and Stage Variables
  - [ ] Caching, Throttling, and Custom Authorizers
- [ ] **Amazon S3 (Developer Focus)**
  - [ ] S3 Presigned URLs for secure temporary uploads/downloads
  - [ ] Lifecycle Policies, Object Locking, and Versioning

## 🔐 Domain 2: Security (26%)
- [ ] **AWS IAM**
  - [ ] IAM Policies structure (Effect, Action, Resource, Condition)
  - [ ] Programmatic access using AWS SDKs and credentials management
  - [ ] Cross-account role assumption (`AssumeRole` API)
- [ ] **Amazon Cognito**
  - [ ] User Pools (Authentication, sign-in/sign-up, JWT tokens)
  - [ ] Identity Pools (Federated Identities, providing temporary AWS credentials)
- [ ] **AWS KMS & Secrets Manager**
  - [ ] KMS Envelope Encryption (`GenerateDataKey` API)
  - [ ] Secrets Manager vs. Parameter Store (Rotation, cross-account, encryption differences)
- [ ] **AWS WAF (Web Application Firewall)**
  - [ ] Attaching WAF to API Gateway or CloudFront to protect endpoints

## 🚀 Domain 3: Deployment (24%)
- [ ] **Infrastructure as Code (IaC)**
  - [ ] **AWS CloudFormation:** Intrinsic functions (`Ref`, `GetAtt`, `FindInMap`), Parameters, Mappings, Outputs
  - [ ] **AWS SAM (Serverless Application Model):** Commands (`sam build`, `sam package`, `sam deploy`), template structure
  - [ ] **AWS CDK (Cloud Development Kit):** Core concepts (Constructs, Stacks)
- [ ] **CI/CD & CodeSuite**
  - [ ] **AWS CodeBuild:** `buildspec.yml` file structure and phases
  - [ ] **AWS CodeDeploy:** AppSpec file (`appspec.yml`), Deployment strategies (Canary, Linear, All-at-Once)
  - [ ] **AWS CodePipeline:** Integrating source control, build, and deploy phases

## 🛠️ Domain 4: Troubleshooting and Optimization (18%)
- [ ] **AWS X-Ray**
  - [ ] Instrumenting application code using the X-Ray SDK
  - [ ] Understanding Segments, Subsegments, Annotations, Metadata, and Sampling rules
- [ ] **Amazon CloudWatch**
  - [ ] CloudWatch Logs, Metric Filters, and Custom Metrics
  - [ ] Implementing CloudWatch Agent on EC2
- [ ] **Amazon ElastiCache**
  - [ ] Redis vs. Memcached architecture and use-cases
  - [ ] Caching strategies (Lazy Loading / Cache-Aside vs. Write-Through)


## Youtube video Freecodecamp

## 🚀 Phase 1: The Core Serverless API (Watch First & Lab)
*Focus on how to build, route, secure, and persist data for a modern application backend.*

* [71:13:06 - AWS Lambda](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=256386s)
* [54:28:20 - Amazon API Gateway](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=196100s)
* [19:29:04 - Amazon DynamoDB](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=70144s)
* [20:32:08 - DynamoDB Follow Along](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=73928s) *(Essential for your hands-on lab)*
* [14:42:50 - Amazon Cognito](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=52970s)

## 📦 Phase 2: Serverless IaC & CI/CD Pipelines (Watch Second)
*Learn how cloud developers define these serverless resources as code and build deployment pipelines.*

* [29:00:37 - Serverless Application Model (SAM)](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=104437s)
* [26:16:37 - AWS CloudFormation](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=94597s)
* [29:05:24 - Continuous Integration Development](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=104724s)
* [29:23:39 - AWS CodeBuild](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=105819s)
* [29:32:20 - AWS CodeDeploy](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=106340s)
* [30:25:22 - AWS CodePipeline](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=109522s)

## 🔍 Phase 3: Observability & Application Security (Watch Third)
*Master application debugging, code-level tracing, and secure secret/key management.*

* [12:20:03 - AWS X-Ray](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=44403s)
* [14:34:48 - Key Management Service (KMS)](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=52488s)
* [19:07:17 - AWS Secrets Manager](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=68837s)
* [19:01:56 - SSM Parameter Store](https://www.youtube.com/watch?v=TTcyhhH2FWE&t=68516s)

## 💡 Tips Clave para el Examen (DVA-C02)

1. **Eficiencia:** AWS siempre quiere la solución que requiera "menos cambios en el código" o sea "más costo-eficiente".
2. **Seguridad:** Nunca pongas credenciales en el código. La respuesta correcta siempre involucra **IAM Roles** o **Secrets Manager**.
3. **DynamoDB:** Si la pregunta menciona "escalabilidad global", la respuesta suele ser **Global Tables**.
4. **Despliegues:** Para evitar downtime total, **Blue/Green** es casi siempre la opción ganadora.

---
**Notas Personales:**
*(Espacio para anotar conceptos que te cuesten más durante el curso)*