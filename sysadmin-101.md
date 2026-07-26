# Checklist: SysAdmin / System Engineer (Enfoque de Estabilidad)

## 1. Dominio Profundo de la Terminal (Consola)
- [ ] **Navegación y Gestión de Archivos:** `find` (con -exec), `locate`, `rsync` (opciones de sincronización, dry-run, exclusión), `tar`, `gzip`/`gunzip`.
- [ ] **Análisis de Discos:** `df -h`, `du -sh *` (entender uso de inodos y bloques), `lsblk`, `fdisk`/`parted`.
- [ ] **Procesamiento de Texto:** `cat`, `less`, `tail -f`, `grep` (reg-ex básico), `sed` (reemplazo en archivos), `awk` (filtrado por columnas).
- [ ] **Permisos y Usuarios:** `chmod` (octal y simbólico), `chown`, `chgrp`, `/etc/passwd`, `/etc/shadow`, `sudoers`.

## 2. Gestión de Sistemas y Servicios (El núcleo)
- [ ] **Systemd:** Crear/editar un archivo `.service`, `systemctl status/start/stop/enable`, consultar logs con `journalctl -u [service] -f`.
- [ ] **Monitoreo de Procesos:** `top`/`htop`, `ps aux`, `kill -9` vs `kill -15`, `lsof -i :[puerto]` (identificar procesos ocupando puertos).
- [ ] **Networking Básico:** `ip addr`, `ip route`, `ss -tulpn` (puertos abiertos), `ping`, `traceroute`, `dig`/`nslookup` (debug de DNS), `curl`/`wget`.
- [ ] **Logs:** Ubicación de logs (`/var/log/syslog`, `/var/log/auth.log`), rotación de logs (`logrotate`).

## 3. Automatización "Scripting" (No copiar, entender)
- [ ] **Bash Shell:** - Variables, condicionales (`if/then/else`), bucles (`for/while`).
    - Redirecciones (`>`, `>>`, `2>`, `&>`).
    - Manejo de argumentos (`$1`, `$2`).
    - **Scripts de Mantenimiento:** Hacer un script que respalde una carpeta, rote los logs, comprima y borre archivos antiguos (usando `find` con `-mtime`).

## 4. Infraestructura como Código (IAc) & Cloud
- [ ] **Terraform:** Desplegar recursos básicos (EC2, S3), gestionar variables y salidas (outputs).
- [ ] **AWS CDK (C#):** Crear el mismo recurso que en Terraform pero usando C#. Entender la síntesis (`cdk synth`).
- [ ] **Configuración:** Entender cómo se diferencian las configuraciones de infraestructura vs. configuraciones de aplicación (env vars).

## 5. Gestión de "Caja Negra" (Resolución de Problemas)
- [ ] **SSH:** Configuración de `~/.ssh/config`, llaves públicas/privadas, copiar llaves con `ssh-copy-id`.
- [ ] **Boot Process:** Entender qué pasa cuando se enciende un servidor (GRUB, runlevels/targets).
- [ ] **Backups:** Estrategias simples (rsync a almacenamiento externo o S3).