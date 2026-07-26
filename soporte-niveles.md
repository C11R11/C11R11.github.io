# Niveles de Atención y Soporte Operacional (ITSM / SRE)

A continuación se detallan los distintos niveles de soporte operacional estructurados por su alcance, criticidad, responsabilidades y las herramientas y tecnologías que se emplean cotidianamente en cada uno de ellos.

---

### 🟢 Nivel 0 (L0): Autoservicio y Automatización (Self-Service)
* **Descripción**: Es el punto de partida donde el usuario o cliente interactúa directamente con sistemas automatizados sin mediación humana. El objetivo principal es desviar tickets repetitivos mediante documentación accesible o flujos autogestionados.
* **Responsabilidades**:
    * Mantenimiento de bases de conocimiento y portales de ayuda.
    * Diseño de árboles de decisión en chatbots o asistentes conversacionales.
    * Habilitar portales de autoservicio para el reinicio de contraseñas o provisión automática de accesos básicos.
* **Tecnologías Cotidianas**: Confluence Knowledge Base, Jira Service Management Portal, ServiceNow, Zendesk Guide, chatbots empresariales (AI-driven).

---

### 🔵 Nivel 1 (L1): Mesa de Ayuda y Soporte Inicial (Help Desk)
* **Descripción**: Es el primer punto de contacto humano para usuarios finales o clientes. Atiende requerimientos básicos, problemas de baja complejidad y se encarga de catalogar, priorizar y derivar los incidentes que no pueden ser resueltos en esta primera línea.
* **Responsabilidades**:
    * Recepción de incidentes por canales formales (tickets, llamadas, chat, correo).
    * Resolución de fallas comunes utilizando manuales o playbooks estandarizados.
    * Escalamiento oportuno de tickets hacia L2 según los Acuerdos de Nivel de Servicio (SLA).
* **Tecnologías Cotidianas**: Jira Service Management, ServiceNow, SysAid, herramientas de acceso remoto (TeamViewer, AnyDesk), Active Directory (operaciones básicas de usuarios).

---

### 🟠 Nivel 2 (L2): Continuidad Operacional y Soporte Técnico Avanzado
* **Descripción**: Nivel de soporte especializado enfocado en el diagnóstico metódico, análisis en caliente de ambientes productivos e infraestructura, y la trazabilidad técnica de errores. No se limita a cerrar tickets, sino a mitigar riesgos de forma proactiva.
* **Responsabilidades**:
    * Análisis avanzado de logs, debug de errores en ambientes de producción e integraciones de API.
    * Troubleshooting técnico *end-to-end* (servidores, redes, bases de datos, contenedores).
    * Gestión de incidentes intermedios/complejos y ejecución de pases de emergencia o configuraciones de infraestructura.
* **Tecnologías Cotidianas**:
    * *ITSM*: ServiceNow, Jira.
    * *Observabilidad/Logs*: Splunk, ELK Stack (Elasticsearch, Logstash, Kibana), GCP Cloud Logging, CloudWatch.
    * *APIs*: Postman, curl.
    * *Infraestructura*: Consola de Linux (Bash), comandos intermedios de Kubernetes (`kubectl`), entornos Docker, Cloud Provider Consoles (AWS/Azure).
    * *Bases de Datos*: SQL Server Management Studio, DBeaver (queries de diagnóstico avanzado).

---

### 🔴 Nivel 3 (L3): Soporte de Ingeniería, Desarrollo y Arquitectura (Bug Fixing)
* **Descripción**: Está conformado por los equipos de desarrollo de software, ingenieros DevOps y arquitectos que construyeron o mantienen el núcleo de la aplicación. Intervienen cuando la resolución del problema requiere cambios profundos en el código base, patches o modificaciones de infraestructura crítica como código.
* **Responsabilidades**:
    * Análisis de causa raíz (RCA - Root Cause Analysis) y corrección definitiva de bugs (*bug fixing*).
    * Diseño y despliegue de parches de software o Hotfixes en producción.
    * Refactorización y mantenimiento preventivo de infraestructura en la nube.
* **Tecnologías Cotidianas**:
    * *Lenguajes/Frameworks*: .NET (C#), Java, Node.js, Python, C++.
    * *Infraestructura como Código (IaC)*: Terraform, AWS CDK, Ansible.
    * *Orquestación y CI/CD*: Kubernetes avanzado, GitHub Actions, Azure Pipelines.
    * *Monitoreo*: Prometheus, Grafana, Datadog, New Relic.

---

### 🟣 Nivel 4 (L4): Soporte Externo y Proveedores (Third-Party Support)
* **Descripción**: Representa la escalación fuera de la organización hacia fabricantes de software, hardware o servicios externos contratados que forman parte del ecosistema operativo de la empresa.
* **Responsabilidades**:
    * Gestión de tickets de soporte empresarial con proveedores externos ante fallas del propio producto (plataformas caídas, bugs del sistema operativo o hardware defectuoso).
    * Coordinación de actualizaciones de seguridad críticas provistas por terceros.
* **Tecnologías Cotidianas**: Portales de soporte oficiales (AWS Enterprise Support, Microsoft Premier, Red Hat Customer Portal, Oracle Support).