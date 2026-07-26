## 📌 Checklist: Fundamentos de APIs REST y Arquitectura Web

Este bloque cubre los conceptos teóricos y de diseño que los entrevistadores técnicos utilizan para medir tu nivel de madurez en el desarrollo de servicios backend robustos.

### 1. Semántica HTTP y Consistencia de Datos
- [ ] **Métodos HTTP: Seguros (Safe) vs. Idempotentes**
  - **Concepto:** Saber diferenciar qué verbos no alteran el estado del servidor (Safe: `GET`, `HEAD`) y cuáles, al ejecutarse múltiples veces con los mismos parámetros, garantizan el mismo estado final del sistema (Idempotent: `GET`, `PUT`, `DELETE`).
  - **Caso de entrevista:** Explicar por qué `POST` no es idempotente (crea un nuevo recurso cada vez) y cómo `PUT` reemplaza completamente el recurso o lo crea con un ID predefinido, garantizando que reintentar la petición sea seguro.
- [ ] **Estrategias de Concurrencia (Optimistic vs. Pessimistic Concurrency)**
  - **Concepto:** Evitar que dos peticiones simultáneas destruyan o pisen datos de forma incorrecta (el problema del "último cambio gana").
  - **Enfoque REST:** Uso de cabeceras HTTP como `ETag` e `If-Match` para implementar concurrencia optimista sin bloquear la base de datos a nivel de fila.

### 2. Robustez y Resiliencia de la API
- [ ] **Manejo Estándar de Errores (Problem Details - RFC 7807)**
  - **Concepto:** No devolver texto plano ni excepciones del sistema crudas al cliente (evita fugas de seguridad). Usar un contrato estandarizado en formato JSON que defina el tipo de error, título, código de estado HTTP y detalles legibles.
  - **En .NET:** Dominar el uso de middlewares globales o la característica nativa `AddProblemDetails()` de .NET Core para homogeneizar las respuestas de error (`400`, `404`, `500`).
- [ ] **Control de Flujo y Seguridad: Rate Limiting y Throttling**
  - **Concepto:** Proteger la infraestructura de saturaciones o ataques de denegación de servicio (DoS) limitando la cantidad de peticiones por usuario, API Key o IP en una ventana de tiempo.
  - **En .NET:** Recordar que desde .NET 7/8 existe el middleware integrado `Microsoft.AspNetCore.RateLimiting` (políticas como *Fixed Window*, *Sliding Window* o *Concurrency Limit*).

### 3. Escalabilidad y Ciclo de Vida
- [ ] **Estatuto de Inmutabilidad (Statelessness)**
  - **Concepto:** Una API REST auténtica debe ser *stateless*. Cada petición HTTP debe contener toda la información necesaria para ser procesada de forma independiente. El servidor no debe guardar sesiones en memoria local (`SessionState`).
  - **Caso de entrevista:** ¿Por qué es vital para escalar? Si la API es *stateless*, puedes poner un balanceador de carga y levantar 10 instancias de tu contenedor Docker; cualquier instancia podrá responder a cualquier cliente sin perder contexto.
- [ ] **Paginación, Filtrado y Ordenamiento Eficiente**
  - **Concepto:** Evitar consultas masivas a la base de datos (`SELECT * FROM Tabla`). Diseñar contratos donde los parámetros `pageNumber`, `pageSize` y `sortBy` limiten la carga de datos desde el controlador.
  - **Técnica:** Diferenciar entre paginación por desplazamiento (*Offset-based* con `Skip`/`Take`) y paginación por cursor (*Keyset-based*, ideal para grandes volúmenes de datos continuos).

### 4. Conceptos Avanzados para Sistemas Distribuidos (Siguiente Nivel)
- [ ] **Manejo de Reintentos en Red: Idempotent Consumer Pattern**
  - **Concepto:** Cuando la red falla, los clientes o los sistemas de mensajería (como colas SQS/RabbitMQ) reintentan el envío. El consumidor debe validar mediante un identificador único (Clave de Idempotencia o *Idempotency Key*) si la transacción ya fue procesada en la base de datos para no duplicar cobros o registros.
- [ ] **Estrategias de Versionamiento de APIs**
  - **Concepto:** Saber cuándo y cómo romper la compatibilidad de una API mediante estrategias de versionamiento: por ruta (ej: `/api/v1/users`), por Query String (ej: `?api-version=2.0`), o por cabeceras personalizadas (`Accept: application/vnd.company.v1+json`).