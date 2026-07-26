# Glosario Operacional: Conceptos y Vocabulario Clave para Soporte L2

## 🛠️ 1. Terminología ITSM & Gestión de Servicios (ITIL)
Conceptos fundamentales sobre el manejo estructurado de incidentes y flujos de trabajo en plataformas como Jira Service Management o ServiceNow.

* **Incidente (Incident):** Interrupción no planificada o reducción de la calidad de un servicio de TI. El objetivo del Soporte L2 es restaurar la normalidad lo antes posible.
* **Problema (Problem):** La causa subyacente o desconocida de uno o más incidentes recurrentes. Requiere investigación profunda para erradicar la raíz.
* **Workaround (Solución Temporal):** Una solución alternativa que reduce o elimina el impacto de un incidente para el cual aún no se dispone de una resolución definitiva (ej. reiniciar un Pod colgado en Kubernetes mientras se busca el memory leak).
* **RCA (Root Cause Analysis - Análisis de Causa Raíz):** Proceso de investigación posterior a un incidente mayor utilizando metodologías estructuradas (como *Los 5 Porqués* o *Diagrama de Ishikawa*) para descubrir exactamente qué falló a nivel técnico o procedimental.
* **Gestión de Cambios (Change Management / Enablement):** Proceso controlado para modificar la infraestructura o el código productivo mitigando riesgos. Involucra la creación de un ticket de cambio, definición de ventanas de mantenimiento y aprobación técnica.
* **Plan de Rollback (Plan de Retorno):** Conjunto detallado de pasos lógicos y comandos necesarios para deshacer un cambio o despliegue fallido y devolver los sistemas a su último estado estable conocido.
* **SLA (Service Level Agreement - Acuerdo de Nivel de Servicio):** Compromiso formal entre el proveedor de servicios y el cliente que define tiempos límites para la respuesta y resolución de incidentes según su severidad (P1, P2, P3, P4).
* **Runbook / Playbook:** Documento técnico procedimental paso a paso que describe detalladamente cómo diagnosticar, mitigar y resolver una alerta o error operativo conocido.

## 🔍 2. Conceptos de Observabilidad & Troubleshooting
Vocabulario asociado a la identificación y rastreo de fallas en arquitecturas distribuidas modernas mediante herramientas como Splunk, ELK o GCP Cloud Logging.

* **Log Estructurado:** Registros de eventos formateados uniformemente (habitualmente en JSON), lo que permite realizar filtros, búsquedas y correlaciones avanzadas de manera eficiente.
* **Correlation ID / Trace ID (Identificador de Correlación):** Código alfanumérico único que se asigna a una petición web en la frontera del sistema y viaja a través de todos los microservicios y bases de datos. Permite la trazabilidad *End-to-End* de un error en entornos distribuidos.
* **Métricas Core de Infraestructura:** * *CPU Throttling:* Limitación que impone el sistema operativo (o Kubernetes) a un contenedor cuando excede el uso de procesamiento asignado.
    * *OOMKilled (Out of Memory Killed):* Evento en el cual el kernel de Linux o el orquestador termina abruptamente un proceso o contenedor por haber excedido el límite estricto de memoria RAM configurado.
* **CrashLoopBackOff:** Estado en Kubernetes que indica que un Pod se inicia, se cae debido a un error de configuración o código, y vuelve a reiniciarse de manera cíclica e infructuosa aumentando el tiempo de espera entre intentos.

## 🌐 3. Conceptos de Redes & APIs REST
Vocabulario esencial para diagnosticar integraciones y problemas de comunicación HTTP usando Postman o inspección de terminal.

* **Semántica REST (Verbos HTTP):** Uso correcto de los métodos de comunicación. `GET` (lectura, seguro), `POST` (creación), `PUT` (reemplazo/actualización), `PATCH` (modificación parcial), `DELETE` (eliminación).
* **Idempotencia (Idempotency):** Propiedad de un endpoint donde realizar la misma petición idéntica múltiples veces produce exactamente el mismo resultado en el estado del servidor que realizarla una sola vez. Crucial en PUT, DELETE y en el procesamiento seguro de colas de eventos duplicados.
* **Bearer Token:** Esquema de autenticación HTTP basado en un token de seguridad (frecuentemente JWT) que se envía en las cabeceras (`Authorization: Bearer <token>`) para dar acceso seguro a los servicios informáticos.
* **Timeouts (Tiempos de Espera):** Límites de tiempo en configuraciones de red. Un *Connection Timeout* ocurre cuando el servidor de destino no responde el saludo inicial; un *Read Timeout* ocurre cuando la conexión se estableció pero el servidor tarda demasiado en retornar los datos.

## 🧠 4. Enfoque Preventivo & Mejora Continua
Términos ligados a la cultura proactiva (Ingeniería Preventiva) demandada en los equipos modernos de alta ingeniería.

* **Monitoreo Reactivo vs. Proactivo:** El enfoque reactivo actúa sólo cuando el usuario avisa que el sistema se cayó; el enfoque proactivo utiliza telemetría avanzada para predecir tendencias anómalas (ej. espacio en disco llenándose linealmente) antes de que afecten la continuidad del negocio.
* **Patrones de Incidentes (Incident Patterns):** Detección metódica de fallos idénticos espaciados en el tiempo (ej. la base de datos se bloquea todos los viernes a las 18:00 hrs), lo que levanta de inmediato la sospecha de un proceso *batch* mal optimizado o un conflicto de *locks* concurrentes.
* **Polly (Resiliencia en Código):** Concepto de incorporar patrones lógicos de tolerancia a fallos directamente en el consumo de APIs o conexiones, tales como políticas de reintentos (*Retry with Exponential Backoff*) y cortacircuitos (*Circuit Breaker*) para evitar caídas en cascada.
* **Sistemas Event-Driven (Guiados por Eventos):** Arquitecturas desacopladas basadas en colas o tópicos de mensajería (como GCP Pub/Sub). Los servicios se comunican asíncronamente mediante la publicación y suscripción de eventos, aislando fallas de disponibilidad inmediata.

# Roadmap de Conceptos y Tecnologías: Soporte Operacional L2

## 🛠️ 1. Soporte & Operación (ITSM)
Marcos de trabajo y procesos estándar para la gestión de servicios de TI.
- [ ] **Gestión de Incidentes:** Flujo completo desde que se detecta una falla (alerta o ticket) hasta que se restaura el servicio (Workaround o solución definitiva). Diferencia entre urgencia, impacto y prioridad.
- [ ] **Gestión de Problemas (RCA):** Análisis de Causa Raíz (Root Cause Analysis). Metodologías para investigar por qué ocurrió un incidente mayor (ej. Los 5 Porqués, Diagrama de Ishikawa) con el fin de que no vuelva a repetirse.
- [ ] **Gestión de Cambios:** Proceso controlado para subir código o modificar configuraciones en ambientes productivos minimizando el riesgo de caídas (ventanas de mantenimiento, planes de rollback).
- [ ] **Herramientas ITSM:** Uso básico de plataformas centralizadas como **Jira Service Management** o **ServiceNow** para el seguimiento y documentación de tickets.

## 🔍 2. Troubleshooting & Observabilidad
Capacidad de diagnosticar y "debuggear" sistemas complejos en caliente de forma metódica.
- [ ] **Análisis Avanzado de Logs:** Búsqueda, filtrado y correlación de trazas de errores utilizando herramientas de agregación. Saber estructurar consultas básicas.
- [ ] **Herramientas de Logs (Al menos una):**
  - **Splunk:** Sintaxis básica de búsqueda (SPL).
  - **ELK Stack (Elasticsearch, Logstash, Kibana):** Creación de filtros y visualización de errores en Kibana.
  - **GCP Cloud Logging:** Filtrado de logs dentro de la consola de Google Cloud.
- [ ] **Trazabilidad End-to-End:** Concepto de seguir una petición de un usuario a través de múltiples microservicios utilizando un identificador único (Correlation ID o Trace ID).

## 🌐 3. APIs & Integraciones
Entendimiento de cómo se comunican las aplicaciones modernas.
- [ ] **Postman:** Configuración de colecciones, ambientes, variables de entorno y ejecución de llamadas HTTP.
- [ ] **Métodos HTTP (Semántica REST):** - `GET` (Lectura), `POST` (Creación), `PUT` / `PATCH` (Actualización), `DELETE` (Eliminación).
- [ ] **Códigos de Respuesta HTTP (Status Codes):**
  - `2xx` (Éxito: 200 OK, 201 Created).
  - `4xx` (Errores del cliente: 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found).
  - `5xx` (Errores del servidor: 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable).
- [ ] **Autenticación en APIs:** Uso de cabeceras HTTP (`Headers`) para enviar credenciales, específicamente el esquema `Authorization: Bearer <token>` (Tokens JWT).

## ⚙️ 4. Kubernetes & Linux
El ecosistema de infraestructura donde corren los servicios.
- [ ] **Conceptos Core de Kubernetes (Nivel Intermedio):**
  - **Pods:** La unidad mínima ejecutable (donde vive el contenedor).
  - **Deployments:** Definición del estado deseado de la aplicación (versión de la imagen, estrategias de actualización).
  - **ReplicaSets (Replicas):** Mecanismo que asegura que siempre haya un número N de Pods idénticos corriendo para alta disponibilidad.
  - **Namespaces:** Aislamiento virtual de recursos dentro del mismo clúster (ej. `prod`, `staging`).
- [ ] **Troubleshooting en Kubernetes (Comandos Fundamentales de `kubectl`):**
  - `kubectl logs <pod-name> -n <namespace>` (Ver las trazas del contenedor).
  - `kubectl describe pod <pod-name> -n <namespace>` (Ver eventos del ciclo de vida del Pod, útil si está en `CrashLoopBackOff` o `ImagePullBackOff`).
  - `kubectl exec -it <pod-name> -n <namespace> -- /bin/sh` (Entrar a la terminal interactiva del contenedor para pruebas).
- [ ] **Linux a nivel Terminal:**
  - Comandos de navegación y visualización: `cd`, `ls`, `cat`, `tail -f` (monitoreo de archivos de logs en vivo), `grep` (filtrado de texto).
  - Gestión de procesos y recursos: `top`, `htop`, `ps aux`, `kill`.

## 💾 5. Bases de Datos (Filtro Excluyente)
Análisis de datos productivos y solución de problemas a nivel de persistencia.
- [ ] **SQL Avanzado:** Consultas complejas, uniones (`JOINs`), subconsultas, funciones de agregación, indexación y optimización de queries.
- [ ] **PL/SQL (Procedural Language/SQL):** Bloques anónimos, creación y debug de Procedimientos Almacenados (`Stored Procedures`), Funciones, Triggers y manejo de Cursores en entornos **Oracle**.
- [ ] **Motores Relacionales:** Particularidades operacionales de **Oracle Database** y **PostgreSQL**.
- [ ] **Troubleshooting en BD:** Diagnóstico de problemas de rendimiento en caliente:
  - Identificación y resolución de bloqueos (**locks** / deadlocks).
  - Monitoreo y terminación de sesiones activas colgadas.
  - Interpretación de códigos de error nativos del motor.

## ☁️ 6. Google Cloud Platform (GCP)
La nube pública que soporta el proyecto.
- [ ] **GCP Pub/Sub:** Entendimiento de arquitecturas basadas en eventos (Event-Driven). Conceptos de Tópicos (`Topics`), Suscripciones (`Subscriptions`), mensajes en cola y reintentos.
- [ ] **GCP Operations Suite:** Monitoreo de métricas con Cloud Monitoring e investigación de alertas operacionales.

## 🧠 7. Competencias y Enfoque (Cultura del Rol)
La mentalidad que evaluará el líder técnico.
- [ ] **Ingeniería Preventiva:** No quedarse en "reiniciar el servicio para que funcione". Identificar patrones repetitivos en las fallas, proponer e implementar alertas automáticas y mantener actualizados los **Runbooks/Runbooks operativos** (guías paso a paso para resolver fallas conocidas) para que el equipo actúe con predictibilidad.