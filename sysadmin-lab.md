# Roadmap: Profesionalizando mi Laboratorio Local

El objetivo es aplicar estándares de la industria a tu infraestructura personal, transformando el `docker-compose` en un sistema resiliente, observable y seguro.

## Fase 1: Control y Calidad (Infraestructura como Código)
- [ ] **Migración a Git-Flow:** Si no lo haces, empieza a trabajar con ramas (`feature/`, `main`) y Pull Requests, incluso para tus propios cambios en el servidor.
- [ ] **Secretos Fuera del Repo:** Nunca subas `.env` al repo. Usa `.env.example` y gestiona los secretos reales con variables de entorno del sistema o un gestor de secretos sencillo (ej: HashiCorp Vault o incluso SOPS).
- [ ] **CI/CD Básico:** Configura un **GitHub Action** que haga un `docker compose build` y `docker compose pull` en tu servidor cada vez que hagas push a `main`.

## Fase 2: Observabilidad y Salud (No vuelvas a preguntar "¿está caído?")
- [ ] **Logging Centralizado:** Deja de entrar por SSH a mirar logs. Instala **Loki** + **Grafana**. Configura el driver de logs de Docker para enviar todo a Loki.
- [ ] **Métricas:** Instala **Prometheus** y el `node_exporter` en tu host. Crea un dashboard en Grafana que te muestre:
    - Uso de CPU y RAM del host y de cada contenedor.
    - Espacio en disco disponible (¡vital antes de que el servidor se detenga!).
- [ ] **Alertas:** Configura un bot de Telegram o alertas por correo simples cuando un contenedor se detenga o el disco pase del 80%.

## Fase 3: Seguridad y Networking (Blindaje)
- [ ] **Reverse Proxy Profesional:** Si usas `docker-compose`, pon **Nginx Proxy Manager** o **Traefik**. Esto te permitirá gestionar certificados SSL (HTTPS) de forma automática con Let's Encrypt para todos tus servicios.
- [ ] **Hardening del Host:** - Desactivar login por contraseña en SSH (usar solo llaves).
    - Instalar `fail2ban` para prevenir ataques de fuerza bruta.
    - Configurar un firewall (`ufw` en Debian) que bloquee todo menos los puertos 80/443 y el puerto de SSH.

## Fase 4: Resiliencia (Backup y Recuperación)
- [ ] **Estrategia de Backup:** No basta con copiar archivos. Automatiza un script que realice:
    - `pg_dump` para bases de datos (no copies los archivos de la BD directamente, es inseguro).
    - Sincronización de volúmenes de Docker hacia un bucket S3 (puedes usar `rclone` para esto).
- [ ] **Plan de Recuperación (Disaster Recovery):** Intenta (una vez) borrar el servidor y levantarlo desde cero usando únicamente tu repositorio de código y tus backups. Si logras levantar todo en menos de 1 hora, estás listo.

## Fase 5: Hacia la Profesionalización (IaC Avanzado)
- [ ] **Ansible:** Deja de configurar el SO manualmente. Escribe un Playbook de Ansible para instalar Docker, configurar el firewall y desplegar tus servicios.
- [ ] **Terraform:** Si eventualmente saltas a la nube, usa Terraform para gestionar los recursos (Cloud VMs, buckets).