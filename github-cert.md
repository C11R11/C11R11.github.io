# 🎯 Checklist de Preparación: GitHub Actions Certification (GH-200)

Este repositorio contiene ejercicios prácticos para cubrir los objetivos del examen oficial.

### 🔄 Comparativa Técnica: Soluciones Ad-hoc vs. Estándar Enterprise

| Concepto | Implementación Ad-hoc (Limitada) | Estándar Enterprise (Examen GH-200) |
| :--- | :--- | :--- |
| **Secretos** | Copiarlos de repo a repo mediante scripts/TF. | Uso de `secrets: inherit` y **Organization Secrets**. |
| **Gobernanza** | Branch Protection configuradas manualmente por repo. | **Repository Rulesets** escalables a nivel de Organización. |
| **Despliegue** | Workflow directo con lógica de rama en el YAML. | **Environments** con **Required Reviewers** y Gatekeepers. |
| **Identidad** | Guardar Access Keys permanentes en Secretos. | **OIDC (OpenID Connect)** con `permissions: id-token: write`. |
| **Reutilización** | Scripts de bash largos embebidos en el YAML. | **Composite Actions** con metadata (`action.yml`) y branding. |

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

# Laboratorios y ejercicios

## 🔐 Laboratorio de Gobernanza y Seguridad
- [ ] **Escenario 1:** Simular PR de colaborador externo usando `pull_request_target`.
- [ ] **Escenario 2:** Implementar `workflow_call` con `secrets: inherit` vs pasar secretos explícitos.
- [ ] **Escenario 3:** Configurar un **Repository Ruleset** que bloquee "Force Pushes" y exija un pipeline exitoso.
- [ ] **Escenario 4:** Restringir los permisos del `GITHUB_TOKEN` a nivel de archivo YAML (`permissions: contents: read`).


ConceptoLo que hiciste (Ad-hoc)Lo que pide el Examen (Enterprise)SecretosCopiarlos de repo a repo.secrets: inherit y Organization Secrets.GobernanzaBranch Protection manual.Repository Rulesets (escalables a nivel Org).DespliegueWorkflow directo a Prod.Environments con Required Reviewers.IdentidadGuardar Access Keys en Secretos.OIDC (OpenID Connect) con permissions: id-token: write.ReutilizaciónScripts de bash largos.Composite Actions con branding e inputs/outputs.ç


# 🧪 Laboratorio de Simulación: GitHub Actions Enterprise (GH-200)

**Objetivo:** Practicar seguridad, gobernanza y centralización de workflows en un entorno simulado de clase empresarial.
**Nota:** Para ejecutar estas funciones gratis, realiza este laboratorio en una **Organización de GitHub** con repositorios **Públicos**.

---

## 🏗️ Ejercicio 1: Herencia de Secretos (Scaling Security)
*En lugar de copiar secretos manualmente con Terraform, usaremos la herencia nativa de GitHub para Workflows Reutilizables.*

- [ ] **Paso A: Crear el Repo Central** Crea un repositorio llamado `central-ops`. Dentro, crea el archivo `.github/workflows/reusable-auth.yml`:

  name: Reusable Auth Workflow
  on:
    workflow_call:
      secrets:
        DB_PASSWORD:
          required: true
  jobs:
    check-connection:
      runs-on: ubuntu-latest
      steps:
        - run: echo "Conectando a la DB con el secreto heredado..."

- [ ] **Paso B: Configurar el Secreto de Organización** Ve a los ajustes de tu Organización -> **Secrets and variables** -> **Actions**. Crea un secreto llamado `DB_PASSWORD`.
- [ ] **Paso C: El Repo Hijo (Consumidor)** En otro repositorio, crea un workflow que llame al central:

  jobs:
    call:
      uses: <tu-org>/central-ops/.github/workflows/reusable-auth.yml@main
      secrets: inherit

- [ ] **Validación:** El job debe completarse exitosamente sin que hayas configurado el secreto en el repo hijo.

---

## 🔐 Ejercicio 2: Ambientes y Gatekeepers (Deployment Control)
*Simular el flujo de aprobación manual que solo existe en niveles Pro/Enterprise para repos privados.*

- [ ] **Paso A: Crear el Environment** En un repo público, ve a **Settings** -> **Environments** -> **New environment**. Llámalo `Production`.
- [ ] **Paso B: Configurar Protecciones**
  - Activa **Required reviewers** y asígnate a ti mismo.
  - Activa **Deployment branch policy** y elige "Selected branches" -> `main`.
- [ ] **Paso C: Workflow de Despliegue** Crea `.github/workflows/deploy.yml`:

  jobs:
    production-deploy:
      environment: Production
      runs-on: ubuntu-latest
      steps:
        - run: echo "Este paso requiere aprobación manual y estar en rama main"

- [ ] **Validación:** Ejecuta el workflow y verifica que se detenga esperando tu clic de aprobación.

---

## 🛡️ Ejercicio 3: Pull Request Target & Privilegio Mínimo
*Entender la vulnerabilidad más común en CI/CD y cómo mitigarla.*

- [ ] **Paso A: Permisos del GITHUB_TOKEN** Crea un workflow y restringe sus permisos de forma explícita:

  permissions:
    contents: read
    pull-requests: write
    id-token: write

- [ ] **Paso B: Escenario de Riesgo** Usa el trigger `on: pull_request_target`.
- [ ] **Reflexión Teórica:** ¿Por qué usar `pull_request_target` es peligroso si haces un `checkout` del código del PR y luego ejecutas un script del mismo? (Respuesta: Porque corre con permisos del repo base, no del fork).

---

## 📋 Ejercicio 4: Repository Rulesets (Modern Governance)
*Sustituir las Branch Protection Rules clásicas por reglas escalables.*

- [ ] **Paso A: Crear el Ruleset** Ve a **Settings** (Org o Repo) -> **Rulesets** -> **New branch ruleset**.
- [ ] **Paso B: Definir Reglas**
  - **Target:** Rama `main`.
  - **Rules:** "Require a pull request before merging" y "Require status checks to pass".
- [ ] **Paso C: Status Checks** Busca el nombre de uno de tus workflows de Actions y agrégalo como check obligatorio.
- [ ] **Paso D: Bypass List** Agrega tu rol de "Admin" a la lista de bypass y pon el Ruleset en modo **Evaluate**.

---

## ⚙️ Ejercicio 5: Composite Actions con Metadata
*Crear componentes de plataforma reutilizables con lógica interna oculta.*

- [ ] **Paso A: Crear action.yml** En una carpeta `custom-linter/`, crea `action.yml`:

  name: 'My Custom Linter'
  inputs:
    path:
      description: 'Directorio a revisar'
      required: true
      default: '.'
  outputs:
    time:
      description: 'Hora de finalización'
  runs:
    using: 'composite'
    steps:
      - run: echo "Revisando archivos en ${{ inputs.path }}"
        shell: bash
      - run: echo "time=$(date)" >> $GITHUB_OUTPUT
        shell: bash
  branding:
    icon: 'check-circle'
    color: 'green'