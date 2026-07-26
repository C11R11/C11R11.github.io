# Guía de Estudio: Administrador Linux (Debian Focus)

Esta guía está diseñada para un aprendizaje incremental, combinando teoría, comandos esenciales y herramientas modernas.

---

## Bloque 1: Cimientos y Operación Linux (Debian)
*El objetivo es sentirte cómodo en la terminal y entender cómo el S.O. gestiona recursos.*

- [ ] **Gestión de Paquetes:** `apt update`, `apt upgrade`, `apt install <paquete>`, `dpkg -i`. Entender repositorios (`/etc/apt/sources.list`).
- [ ] **Permisos y Usuarios:** `chmod`, `chown`, `useradd`, `usermod`, edición de `/etc/sudoers` para seguridad.
- [ ] **Gestión de Servicios:** `systemctl` (start, stop, status, enable). Crear y entender archivos `.service` en `/etc/systemd/system/`.
- [ ] **Monitoreo Core:**
    - `top`/`htop`: Procesos y carga de CPU.
    - `iostat`/`vmstat`: I/O y memoria.
    - `ss`/`netstat`: Conexiones activas y puertos abiertos.
- [ ] **Análisis de Logs:** Ubicación en `/var/log/`, uso de `journalctl -u <servicio>` y `tail -f`.

## Bloque 2: Web Server & Aplicaciones
*Dominio de Nginx y gestión de entornos de ejecución.*

- [ ] **Nginx:** Configuración de Virtual Hosts, manejo de Reverse Proxy para aplicaciones, configuración de SSL (Certbot/Let's Encrypt).
- [ ] **PHP-FPM:** Configuración de pools, ajuste de workers y límites de memoria en `/etc/php/x.x/fpm/`.
- [ ] **Despliegue:**
    - **Node.js:** Gestión con `pm2` (iniciar, listar, logs, restart).
    - **Python:** Uso de entornos virtuales (`venv`), instalación de dependencias con `pip`.
- [ ] **Wordpress/Symfony:** Estructura de directorios típica, manejo de permisos de escritura (`www-data`).

## Bloque 3: Bases de Datos (Escalabilidad y HA)
*Entender la persistencia fuera de la máquina local.*

- [ ] **MySQL/PostgreSQL:** Comandos básicos de cliente (`mysql -u -p`, `psql -U -d`).
- [ ] **Configuración:** Edición de archivos de configuración para aceptar conexiones externas (`listen_addresses`, `bind-address`).
- [ ] **Alta Disponibilidad:**
    - Concepto de Replicación Master-Slave (Lectura/Escritura).
    - Configuración de clústeres.
    - Estrategias de Failover (¿Qué pasa si el master cae?).

## Bloque 4: Cloud, Seguridad y Secretos
*Habilidades exigidas por la oferta de trabajo.*

- [ ] **Cloud:** Gestión básica en Vultr/DigitalOcean (Firewalls de red, snapshots).
- [ ] **HashiCorp Vault:**
    - Concepto: Centralización de credenciales.
    - Operativa: Autenticación, cómo leer un secreto desde un script/app sin hardcodear.
- [ ] **Hardening de Servidores:**
    - Desactivar acceso root por SSH.
    - Uso de llaves SSH (no contraseñas).
    - Configuración de `ufw` (firewall básico).
- [ ] **Respaldo (S3 Compatible):** Uso de `rclone` o herramientas de backup para sincronizar directorios a buckets S3.

## Bloque 5: Automatización e Infraestructura (IaC)
*Aquí es donde pasas de ser un administrador a un ingeniero de plataforma.*

- [ ] **Ansible:**
    - Inventarios, Playbooks básicos.
    - Automatizar el "hardened" de un servidor nuevo.
- [ ] **Terraform:**
    - Definir infraestructura (droplets, buckets, redes).
    - Concepto de `terraform plan` y `terraform apply`.
- [ ] **Observabilidad:**
    - Prometheus: Instalar `node_exporter`.
    - Grafana: Crear un dashboard sencillo para visualizar métricas del sistema.
    - Loki: Entender cómo centralizar logs.

---

### Tips para el Avance Diario:
1. **No sobrecargues:** Estudia un bloque por semana.
2. **Documenta:** Por cada comando que aprendas, escríbelo en un script `.sh` de prueba.
3. **Refuerzo:** Si te sientes bloqueado, usa el enfoque "Break-Glass": busca cómo solucionar ese problema específico en la documentación oficial (ej: documentación de Debian o Nginx).
4. **Mentalidad:** Recuerda que tu valor Senior es **entender el sistema completo**. No te limites a copiar comandos, pregunta siempre "¿Por qué este servicio falla?".