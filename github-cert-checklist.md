# GH-200 Study Guide: Domain 1 Checklist (with Code Anchors)

## 1. Author and Manage Workflows (20–25%)

### Configure workflow triggers and events
- [x] **Configure workflows to run for scheduled, manual, webhook, and repository events** (`on: schedule: - cron:`, `on: workflow_dispatch:`, `on: push:`, `on: pull_request:`)

[Workflow making notes](github-workflows-make.md#author-and-manage-workflows)

- [x] **Define and validate workflow_dispatch inputs (types, required, defaults) and pass inputs to reusable workflows via workflow_call with inputs and secrets mapping** (`inputs: name: type: string, default: 'val'`, `with: arg: ${{ inputs.name }}`, `secrets: inherit`)

[Workflow making notes](github-workflows-make.md#dispatch)

### Design and implement workflow structure
- [x] **Use jobs, steps, and conditional logic** (`jobs:`, `steps:`, `if: github.event_name == 'push'`)

- [x] **Implement dependencies between jobs** (`needs: [job_a, job_b]`)

- [x] **Use workflow commands and environment variables** (`echo "VAR=VAL" >> $GITHUB_ENV`, `::error::message`)

[Workflow making notes](github-workflows-make.md#workflow-commands)

- [x] **Use service containers (services:) for dependent services (databases, queues); configure ports, health checks, and container options** (`services: redis: image: redis`, `ports: ["6379:6379"]`, `options: "--health-cmd ..."`)

[Job And Service containers](github-containers.md)

- [x] **Implement YAML anchors and aliases (&, * and merge <<) to reuse repeated mappings/steps within a single workflow file** (`env: &defaults`, `env: *defaults`, `<<: *defaults`)

[Workflow making notes](github-workflows-make.md#anchors-and-aliases)

- [x] **Use strategy and matrix to generate job variations (OS, language/runtime versions); apply include/exclude; control fail-fast and max-parallel; optimize matrix size for cost and performance; account for runner image changes (Ubuntu 20.04 deprecation, Windows Server 2025 migration for windows-latest)** (`strategy: matrix: node: [18, 20]`, `include:`, `exclude:`, `fail-fast: false`, `runs-on: windows-2025`)

[Github Matrix context](github-matrix.md)

- [x] **Evaluate expressions with ${{ }} referencing contexts; distinguish static (workflow parse) vs runtime evaluation; prevent secret leakage in logs and expressions** (`if: ${{ github.event_name == 'push' }}`, `env: PASSWORD: ${{ secrets.PW }}`)

[Github Contexts](github-contexts.md)
[Workflow making notes](github-workflows-make.md#expressions)

- [x] **Leverage editor tooling (GitHub Actions VS Code extension / YAML schema completion, metadata IntelliSense, validation) to author and maintain workflows efficiently** (Action/YAML linting extension in VS Code)

[Workflow making notes](github-workflows-make.md#tooling)

### Manage workflow execution and outputs
- [ ] **Configure caching and artifact management; apply retention policies via REST APIs (logs, artifacts, workflow runs) at org/repo level** (`uses: actions/cache@v4`, `uses: actions/upload-artifact@v4`, `retention-days: 30`)
- [ ] **Pass data between jobs and steps (artifacts, outputs, environment files via GITHUB_ENV and GITHUB_OUTPUT, reusable workflow outputs)** (`echo "val=foo" >> $GITHUB_OUTPUT`, `outputs: my_out: ${{ steps.id.outputs.val }}`)
- [ ] **Generate job summaries using GITHUB_STEP_SUMMARY for rich Markdown reports (test results, coverage, links)** (`echo "### Test Passed" >> $GITHUB_STEP_SUMMARY`)
- [ ] **Add workflow status badges and environment protections** (`![Badge](https://github.com/...)`, `environment: production`)

[]()

## 2. Consume and troubleshoot workflows (15–20%)

### Interpret workflow behavior and results
- [ ] **Identify workflow triggers and effects from configuration and logs** (Check `github.event_name` in logs / UI "Triggered by" label)
- [ ] **Diagnose failed workflow runs using logs and run history** (Check red ❌ status, "Annotations" tab, or `gh run view --log`)
- [ ] **Expand and interpret YAML anchors, aliases, and merged mappings when analyzing workflow configuration** (`&anchor_name`, `*alias_reference`, `<<: *merge_map`)
- [ ] **Interpret matrix expansions, correlate job names to matrix axes, analyze failures across variants, and selectively rerun individual matrix jobs** (UI: "Jobs" sidebar list; "Re-run failed jobs" button)

> Beyond running workflows on feature branches, here are key tasks where the CLI outperforms or provides unique access compared to the Web UI:

> * Watching Live Logs: You can use gh run watch to stream live logs directly to your terminal as a job executes. In the Web UI, you are stuck with the browser's refresh rate and interface.

> * Bulk Secret Management: You can set secrets from a file or local environment variable using gh secret set -f .env. Doing this in the Web UI requires manual entry for every single secret.

> * API Prototyping: The gh api command allows you to interact with any GitHub endpoint—including experimental ones—without writing a full script or using a tool like Postman.

> * Local Repository Setup: You can create a remote repository and push your local code in one command with gh repo create --source=. --push. The Web UI requires you to create the repo first, then manually run the git remote add commands.

> * Workflow Performance Data: Use gh run view --json to get a machine-readable summary of job duration and success rates. This is much harder to aggregate manually via the UI.

### GitHub CLI vs. Web UI: The Admin Cheat Sheet

| Feature | GitHub CLI (`gh`) | Web UI (Browser) |
| :--- | :--- | :--- |
| **Workflow Dispatch** | Defaults to your **current local branch** automatically. | Requires manual selection from a dropdown menu. |
| **Log Monitoring** | `gh run watch` streams live interactive logs to your terminal. | Static page that requires browser refreshes or waiting for the UI to update. |
| **Secret Management** | `gh secret set -f .env` allows for **bulk uploads** from files. | Requires manual "New Secret" entry for every individual key-value pair. |
| **Repo Creation** | One command to create remote, add origin, and push: `gh repo create --push`. | Multi-step process involving browser clicks and manual `git` commands in the terminal. |
| **Auth Verification** | `gh auth status` instantly lists your token's active scopes and permissions. | Requires navigating through several layers of Personal Settings to find the PAT. |
| **Error Handling** | Provides machine-readable JSON output for scripting (`--json`). | Visual-only; requires manual copying/pasting for documentation or scripts. |

### Access workflow artifacts and logs
- [ ] **Locate workflows, logs, and artifacts in the UI and via API** (`GET /repos/{owner}/{repo}/actions/runs`, `gh run list`)
- [ ] **Download and manage workflow artifacts** (`uses: actions/download-artifact@v4`, `gh run download <run_id>`)

### Use and manage workflow templates
- [ ] **Consume organization-level and reusable workflows** (`uses: <org>/<repo>/.github/workflows/<file>.yml@<ref>`)
- [ ] **Consume non-public organization workflow templates** (Settings -> Actions -> General -> "Allow all actions and reusable workflows")
- [ ] **Use starter workflows (public and private/non-public templates); customize and adapt; distinguish from reusable workflows and composite actions** (`.github/workflow-templates/*.yml` in the `.github` repo)

(Workflow templates reference)[https://docs.github.com/en/enterprise-cloud@latest/actions/reference/workflows-and-actions/reusing-workflow-configurations#workflow-templates]

- [ ] **Differentiate starter workflows (copy scaffold, independent after creation) vs reusable workflows (central versioned definition invoked via workflow_call) vs composite actions (encapsulated step logic)** (Copy/Paste UI gallery vs `workflow_call` vs `runs: using: 'composite'`)
- [ ] **Contrast disabling and deleting workflows** (Actions -> "..." menu -> "Disable workflow" vs "Delete workflow run")

### 🔄 Reusable Workflow: Reference Guide

| Reference Type | Syntax | Best Use Case |
| :--- | :--- | :--- |
| **Local (Relative)** | `uses: ./.github/workflows/file.yml` | Workflows in the **same repo**. Follows your current branch/commit automatically. |
| **Remote (Tagged)** | `uses: org/repo/path@v1` | Centralized libraries. Ensures you use a **stable, versioned** release. |
| **Remote (SHA)** | `uses: org/repo/path@sha123...` | **Maximum Security.** Prevents any changes to the called code. |

## ⚖️ Composite Actions vs. Reusable Workflows

While both promote the "DRY" (Don't Repeat Yourself) principle, they operate at different levels of the GitHub Actions hierarchy.

| Feature | **Composite Action** (The "Step") | **Reusable Workflow** (The "Job") |
| :--- | :--- | :--- |
| **Hierarchy** | A collection of **steps**. | A collection of **jobs**. |
| **Implementation** | Called inside a job's `steps:` list. | Called at the `jobs:` level. |
| **Runner (`runs-on`)** | Uses the runner defined by the **caller**. | Defines its **own** runner independently. |
| **UI Appearance** | All steps are grouped under one heading. | Appears as a distinct job in the workflow map. |
| **Secrets** | Can access `secrets` directly (if in same repo). | Must be passed explicitly or via `secrets: inherit`. |
| **Best For...** | Standardizing a specific "task" (e.g., Setup). | Standardizing a "pipeline" (e.g., Production Deploy). |

### 🏛️ Standardized Workflow Architecture

1. **DRY at the Root**: Use **Composite Actions** for low-level tasks (auth, tool installation, cleanup) to ensure you never write the same `run:` script twice.
2. **Standardize the Job**: Wrap those actions in **Reusable Workflows** to define the "Company Way" of testing or deploying.
3. **Version Everything**: Use semantic tags (e.g., `@v1`) for your library so you can push updates without breaking everyone's pipelines at once.
4. **Abstract Complexity**: The goal is to make the developer's local workflow file as short as possible.

### 🔄 Reusability: When to use what?

| Use Case | Recommended Tool | Why? |
| :--- | :--- | :--- |
| **Standardizing a "Setup"** | **Composite Action** | Allows developers to add their own tests/steps after the setup. |
| **Standardizing a "Deployment"** | **Reusable Workflow** | Ensures the entire job (including environment/concurrency) is locked down. |
| **Complex Logic (if/else)** | **Reusable Workflow** | Supports job-level conditionals and multiple related jobs. |
| **Speed/Simplicity** | **Composite Action** | Faster to implement; doesn't require separate job overhead. |

#### 🎓 GH-200 Exam Tip
If the question mentions **"Calling a workflow from another repository to standardize a whole CI/CD pipeline,"** the answer is **Reusable Workflow**. 
If it mentions **"Bundling multiple run commands into a single step,"** the answer is **Composite Action**.

## 3. Author and maintain actions (15–20%)

### Create and troubleshoot custom actions
- [ ] **Identify and implement action types (JavaScript, Docker, composite); understand immutable actions rollout on hosted runners and implications for version pinning and registry sources** (`using: 'node20'`, `using: 'docker'`, `using: 'composite'`; `uses: actions/checkout@v4` vs `@SHA`)
- [ ] **Troubleshoot action execution and errors** (Inspect `dist/` for JS actions, check `ENTRYPOINT` for Docker, and shell context for Composite)

> * In a Composite Action, the shell: bash line is mandatory for every run step. In a standard Workflow job, it is optional (it defaults to bash on Linux), but the GH-200 exam loves to trick you by omitting it in a Composite Action.

### Define action structure and metadata
- [ ] **Specify required files, directory structure, and metadata** (`action.yml` file, `inputs:`, `outputs:`, `runs:`)
- [ ] **Implement workflow commands within actions** (JS: `core.setFailed()`, `core.setOutput()`; Bash: `echo "::set-output name=x::y"`)

### Distribute and maintain actions
- [ ] **Select distribution models (public, private, marketplace)** (Public repo for marketplace vs. Internal/Private repo for Org-only)
- [ ] **Publish actions to the GitHub Marketplace** (`branding: icon: 'check-circle', color: 'blue'`)
- [ ] **Apply versioning and release strategies** (`git tag -a v1.0.0`, `git tag -fa v1 -m "Update v1 tag"`)

### 🏷️ How to Version your Actions/Workflows

| Action | Command |
| :--- | :--- |
| **Create Tag** | `git tag -a v1 -m "Release v1"` |
| **Push Tag** | `git push origin v1` |
| **Update Tag** | `git tag -fa v1 -m "Update v1 to latest"; git push origin v1 --force` |
| **Delete Tag** | `git tag -d v1; git push origin --delete v1` |

### 🔄 Maintenance Strategy: Monorepo vs. Polyrepo

| Feature | Monorepo (Standard) | Polyrepo (Granular) |
| :--- | :--- | :--- |
| **Versioning** | Single tag for all tools. | Unique tag per tool. |
| **Maintenance** | Low (One repo to manage). | High (Many repos/permissions). |
| **Dependency** | Easy to sync related tools. | Complex to sync across repos. |
| **Use Case** | Internal platform teams. | Public/Open-source actions. |

---

### 🏷️ The "Moving Tag" Workflow (The GH-200 Way)

To maintain a centralized `@v1` tag that always points to the latest stable release:

1. **Tag the specific version:** `git tag v1.0.5`
2. **Force-move the major tag:** `git tag -fa v1 -m "Moving v1 tag"`
3. **Push to GitHub:** `git push origin v1 --force`

*Note: This is why SHA pinning is safer for production; it protects against the DevOps team accidentally moving a tag to a broken version.*

## 4. Manage GitHub Actions for the enterprise (20–25%)

### Distribute and govern actions and workflows
- [ ] **Define and manage reusable components and templates** (`on: workflow_call:`, `.github/workflow-templates/`)
- [ ] **Control access to actions and workflows within the enterprise** (Enterprise Settings -> Policies -> Actions -> "Allowed actions")
- [ ] **Configure organizational use policies** (Settings -> Actions -> General -> "Allow all", "Allow select actions", "Allow verified creators")

### Manage runners at scale
- [ ] **Configure and monitor GitHub-hosted and self-hosted runners** (`runs-on: ubuntu-latest` vs `./config.sh --url ... --token ...`)
- [ ] **Apply IP allow lists and networking settings** (Enterprise Settings -> "IP allow list" for Runner API/Actions traffic)
- [ ] **Manage runner groups and troubleshoot runner issues** (Settings -> Actions -> Runner Groups -> "Selected repositories"; check `_diag` folder for logs)
- [ ] **Identify preinstalled software/tool versions on GitHub-hosted runners (image release notes, toolcache) and install additional software at runtime (setup-* actions, package managers, caching, container images, custom self-hosted images)** (Check `runner-images` repo, `uses: actions/setup-node@v4`, `sudo apt-get install`, `uses: actions/cache@v4`)

### Manage encrypted secrets and variables
- [ ] **Define and scope encrypted secrets and variables at the organization, repository, and environment levels** (Settings -> Secrets and variables -> Actions; UI scoping options)
- [ ] **Access and use secrets and variables in workflows and actions; manage secrets and variables programmatically via REST APIs** (`${{ secrets.ORG_SECRET }}`, `${{ vars.GLOBAL_VAR }}`, `gh secret set <name> --org <org_name>`)

## 5. Secure and optimize automation (10–15%)

[Github security](github-security.md)

### Implement security best practices
- [ ] **Use environment protections and approval gates** (`environment: production`, "Required reviewers", "Wait timer")
- [ ] **Identify and use trustworthy actions from the Marketplace** (Look for the "Verified Creator" blue badge ✅)
- [ ] **Mitigate script injection (sanitize/validate inputs, least-privilege permissions, avoid untrusted data in run:, proper shell quoting, prefer vetted actions over inline scripts)** (Use `env` variables for `${{ github.event... }}` instead of direct expansion in `run:`)
- [ ] **Understand GITHUB_TOKEN lifecycle (ephemeral, scoped), configure granular permissions, contrast with PAT; restrict write scopes** (`permissions: contents: read`, `pull-requests: write`)

# 🛡️ ShellCheck & GitHub Contexts

When writing `run:` scripts, directly placing `${{ github.xxx }}` inside a shell script can cause linting errors (SC2193) and security vulnerabilities (Shell Injection).

## ✅ The "Env Map" Pattern
Always map the GitHub Context to an environment variable in the `env:` block of the step before using it in a `run:` script.

**Why do this?**
1. **Linting:** `actionlint` and `shellcheck` can now validate the shell logic properly.
2. **Security:** It prevents attackers from injecting malicious code if the context variable contains special shell characters (like `;` or `&&`).
3. **Readability:** Your shell script looks like a standard script.

## 📋 Fixed Syntax Example
```yaml
- run: echo "User is $USER_NAME"
  env:
    USER_NAME: ${{ github.actor }}

> * Token Scoping: Fine-grained PATs must have the Organization as the Resource Owner to modify Org repos; otherwise, you get the 403 you encountered.
> * The "Metadata" Base: Every token needs at least metadata: read to even see a repository's exists via the API.
> * To create repos you need the Administration and Content permission (read and write)

- [ ] **Use OIDC token (id-token permission) for cloud provider federation to eliminate long-lived cloud secrets** (`permissions: id-token: write`, `uses: aws-actions/configure-aws-credentials@v4`)
- [ ] **Pin third-party actions to full commit SHAs; align with immutable actions enforcement on hosted runners; avoid floating @main/@v* without justification** (`uses: actions/checkout@a5ac7e51b41094c92402da3b24376905380afc29`)
- [ ] **Enforce action usage policies (organization/repository allow/deny lists, required reviewers for unverified actions)** (Org Settings -> Actions -> General -> "Allow select actions")
- [ ] **Generate and verify artifact attestations / provenance (e.g., SLSA, build metadata) and integrate into deployment verification** (`uses: actions/attest-build-provenance@v1`, `gh attestation verify`)

### Optimize workflow performance and cost
- [ ] **Configure caching and artifact retention for efficiency; apply retention policies programmatically via REST APIs** (`uses: actions/cache@v4`, `retention-days: 5`, `DELETE /repos/{owner}/{repo}/actions/artifacts/{artifact_id}`)
- [ ] **Recommend strategies for scaling and optimizing workflows** (Parallelize via `matrix`, use `concurrency` groups, minimize `fetch-depth: 0` in checkout)