* [ AWS Cli](aws-cli.md) - Aws cli 
* [ AWS Lambda](aws-lambda.md) - Aws lambda


# 🚀 Plan de Choque: AWS Certified Developer Associate (DVA-C02)

**Objetivo:** Aprobar el examen en tiempo récord priorizando el perfil de Platform Engineering y SDLC.
**Curso de referencia:** Stephane Maarek (Udemy).

---

## 📅 SEMANA 1: El Núcleo del Examen (60-70% del puntaje)

### Día 1-2: Serverless & Seguridad de Aplicaciones
*Focus: Cómo corre el código sin servidores y cómo se accede de forma segura.*
- [ ] **AWS Lambda:** Configuración, límites (15 min, 10GB), variables de entorno, capas (layers) y versiones/alias.
- [ ] **Amazon API Gateway:** Tipos de API, integraciones, Throttling, y Lambda Authorizers.
- [ ] **AWS Cognito:** User Pools (autenticación) vs Identity Pools (autorización).
- [ ] **IAM para Developers:** Roles de ejecución vs. Políticas de usuario.

### Día 3: Base de Datos NoSQL (El "hueso" del examen)
*Focus: Consultas eficientes y escalabilidad.*
- [ ] **DynamoDB Fundamentos:** RCU/WCU (Cálculos de capacidad), particiones y claves.
- [ ] **Optimización:** Query vs Scan (Nunca elijas Scan si puedes evitarlo).
- [ ] **Índices:** LSI (Local Secondary Index) vs GSI (Global Secondary Index).
- [ ] **DynamoDB Streams:** Integración con Lambda.

### Día 4: CI/CD y Automatización (Tu zona de confort)
*Focus: El "Golden Path" de AWS.*
- [ ] **AWS CodeSuite:** CodeCommit, CodeBuild (buildspec.yml), CodeDeploy (appspec.yml) y CodePipeline.
- [ ] **Estrategias de Despliegue:** Blue/Green, Canary, Rolling, All-at-once (Entender cuándo usar cada una en **Elastic Beanstalk** y **ECS**).
- [ ] **CloudFormation:** Conceptos básicos, Secciones (Resources, Parameters, Outputs) y CloudFormation StackSets.

---

## 📅 SEMANA 2: Monitoreo, Debugging y Refuerzo

### Día 5: Observabilidad y Configuración
*Focus: Herramientas para ayudar a los desarrolladores a debugear.*
- [ ] **AWS X-Ray:** Instrumentación de código, segmentos, subsegmentos y anotaciones (Tracing).
- [ ] **Amazon CloudWatch:** Logs, Metrics, Alarms y Events (EventBridge).
- [ ] **AWS KMS & Secrets Manager:** Encriptación de datos, rotación de secretos y diferencia con Parameter Store.

### Día 6: Otros Servicios y Casos de Uso
*Focus: Servicios de mensajería y desacoplamiento.*
- [ ] **SQS (Simple Queue Service):** Visibilidad, Dead Letter Queues, Standard vs FIFO.
- [ ] **SNS (Simple Notification Service):** Pub/Sub y Fan-out.
- [ ] **Kinesis:** Streaming de datos en tiempo real (Shards).

### Día 7: Simulación de Examen y Cierre
*Focus: Mentalidad de examen.*
- [ ] **Examen de Práctica 1:** Analizar cada respuesta fallida.
- [ ] **Repaso de "Cheat Sheets":** Revisar límites de servicios y comparativas de costos.
- [ ] **Agendar Examen:** Si sacas +75% en los simuladores, estás listo.

---

## 💡 Tips Clave para el Examen (DVA-C02)

1. **Eficiencia:** AWS siempre quiere la solución que requiera "menos cambios en el código" o sea "más costo-eficiente".
2. **Seguridad:** Nunca pongas credenciales en el código. La respuesta correcta siempre involucra **IAM Roles** o **Secrets Manager**.
3. **DynamoDB:** Si la pregunta menciona "escalabilidad global", la respuesta suele ser **Global Tables**.
4. **Despliegues:** Para evitar downtime total, **Blue/Green** es casi siempre la opción ganadora.

---
**Notas Personales:**
*(Espacio para anotar conceptos que te cuesten más durante el curso)*