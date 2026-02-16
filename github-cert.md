# 🎯 Checklist de Preparación: GitHub Actions Certification (GH-200)

Este repositorio contiene ejercicios prácticos para cubrir los objetivos del examen oficial.

---

## 🛠️ Dominio 1: Author and Maintain Workflows (40%)
*Focus: Sintaxis de YAML y lógica de ejecución.*

- [ ] **Eventos de Disparo (Triggers):** Configurar `push`, `pull_request`, `schedule` (cron) y `workflow_dispatch`.
- [ ] **Filtros de Eventos:** Uso de `branches`, `paths` y `tags` para limitar la ejecución.
- [ ] **Sintaxis de Jobs y Steps:** Configuración de `id`, `name`, `uses`, `run` y `with`.
- [ ] **Contextos y Expresiones:** Manejo de `${{ github }}`, `${{ secrets }}`, `${{ env }}` y `${{ matrix }}`.
- [ ] **Funciones de Estado:** Uso de `if: success()`, `failure()`, `always()` y `cancelled()`.
- [ ] **Estrategias de Matriz:** Configuración de `strategy: matrix` con `include` y `exclude` para pruebas multiplataforma.
- [ ] **Dependencias entre Jobs:** Uso de `needs` para orquestar la ejecución secuencial.
- [ ] **Ambientes y Aprobaciones:** Configuración de `environment` para despliegues protegidos.

---

## 🏗️ Dominio 2: Author and Maintain Actions (25%)
*Focus: Creación de componentes reutilizables (Tu mayor área de oportunidad).*

- [ ] **Metadata de la Acción:** Estructura completa de `action.yml` (inputs, outputs, runs).
- [ ] **Acciones de Docker:** Creación de un `Dockerfile` y punto de entrada para acciones en contenedores.
- [ ] **Acciones de JavaScript:** Estructura de archivos, manejo de dependencias (`node_modules`) y `main.js`.
- [ ] **Acciones Compuestas (Composite):** Agrupación de múltiples pasos de shell en una sola acción reutilizable.
- [ ] **Comandos de Workflow:** Uso de comandos de shell para interactuar con el runner (ej. establecer variables de salida).
- [ ] **Branding:** Configuración de iconos y colores en `action.yml` para el Marketplace.

---

## 🔍 Dominio 3: Consume and Troubleshoot Workflows (20%)
*Focus: Depuración, Logs y Optimización.*

- [ ] **Uso de Acciones del Marketplace:** Sintaxis de versionamiento (`v1`, `v1.2.3`, `SHA`).
- [ ] **Troubleshooting:** Habilitación de `ACTIONS_STEP_DEBUG` y `ACTIONS_RUNNER_DEBUG`.
- [ ] **Artifacts:** Uso de `upload-artifact` y `download-artifact` para persistir datos entre jobs.
- [ ] **Caching:** Configuración de `actions/cache` para acelerar dependencias (npm, pip, etc.).
- [ ] **Límites de Ejecución:** Entender los límites de uso por tiempo y storage.

---

## 🏢 Dominio 4: Manage GitHub Actions for the Enterprise (15%)
*Focus: Gobernanza, Seguridad y Runners.*

- [ ] **Self-hosted Runners:** Instalación, etiquetas (labels) y mantenimiento de runners propios.
- [ ] **Políticas de Seguridad:** Configuración de permisos del `GITHUB_TOKEN` en el workflow.
- [ ] **Secret Scanning:** Protección contra exposición de credenciales.
- [ ] **Workflows Reutilizables:** Llamadas a workflows externos con `workflow_call`.
- [ ] **Gobernanza Organizacional:** Limitación de acciones permitidas a nivel de organización o repositorio.

---

## 🚀 Próximos Pasos sugeridos
1. **Repo Setup:** Crea un flujo que falle a propósito y usa `if: failure()` para notificar.
2. **Lab de Acciones:** Crea una **Composite Action** que use un secreto y devuelva un output.
3. **Lab de Runners:** Si tienes Docker local, intenta simular un self-hosted runner.