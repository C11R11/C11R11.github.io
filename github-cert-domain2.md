> Beyond running workflows on feature branches, here are key tasks where the CLI outperforms or provides unique access compared to the Web UI:

> * Watching Live Logs: You can use gh run watch to stream live logs directly to your terminal as a job executes. In the Web UI, you are stuck with the browser's refresh rate and interface.

> * Bulk Secret Management: You can set secrets from a file or local environment variable using gh secret set -f .env. Doing this in the Web UI requires manual entry for every single secret.

> * API Prototyping: The gh api command allows you to interact with any GitHub endpoint—including experimental ones—without writing a full script or using a tool like Postman.

> * Local Repository Setup: You can create a remote repository and push your local code in one command with gh repo create --source=. --push. The Web UI requires you to create the repo first, then manually run the git remote add commands.

> * Workflow Performance Data: Use gh run view --json to get a machine-readable summary of job duration and success rates. This is much harder to aggregate manually via the UI.

# 📈 Improving Workflow Observability

Instead of digging through thousands of lines of logs, use **Job Summaries** to surface important data like cache sizes or test results.

## 📝 The `$GITHUB_STEP_SUMMARY`
* **Function:** A special environment file that accepts Markdown.
* **Visibility:** Content written to this file appears on the "Summary" page of the GitHub Action run.
* **DevOps Value:** Quickly identifies "Cache Bloat." If your `node_modules` jumps from 200MB to 1GB, you know a developer added a massive dependency that might slow down the entire CI/CD pipeline.

## ⚖️ Performance Monitoring
| Metric | Action |
| :--- | :--- |
| **Cache Hit** | Skip `npm install` (Saves 1-3 minutes). |
| **Cache Miss** | Run `npm install` + Save new Cache. |
| **Bloat Check** | Monitor if cache size exceeds ~500MB (Consider trimming devDependencies). |

# 🧨 Cache Invalidation (Cache Busting)

In GitHub Actions, caches are **immutable**. Once a cache is saved for a specific `key`, it cannot be updated. You can only create a *new* key.

## When to "Bust" the Cache
1. **Tooling Update:** You changed the version of Node or the build tool (e.g., switching from `npm` to `pnpm`).
2. **Corruption:** A previous build failed mid-install and saved a broken `node_modules`.
3. **Logic Change:** You changed the path being cached (e.g., from `~/.npm` to `./node_modules`).

## 🛠️ Implementation Strategy
* **Best Practice:** Always include a version prefix in your keys (e.g., `cache-v1-...`).
* **Action:** To clear the cache for the entire organization/repo, increment the prefix to `v2`.

# 🔍 Context Variables in workflow_run

When a workflow is triggered by `workflow_run`, you have access to the `github.event.workflow_run` object. Here are the most useful properties:

| Property | Description |
| :--- | :--- |
| `${{ github.event.workflow_run.id }}` | The ID of the Parent run (useful for API calls). |
| `${{ github.event.workflow_run.conclusion }}` | Can be `success`, `failure`, `cancelled`, or `skipped`. |
| `${{ github.event.workflow_run.html_url }}` | Link to the Parent's logs. |
| `${{ github.event.workflow_run.head_sha }}` | The commit SHA that triggered the Parent. |

## ⚠️ Important Behavior
The `Child` workflow (Compliance) always runs using the code from the **default branch** (main), even if the `Parent` was triggered by a feature branch. This ensures that compliance/reporting scripts cannot be "tampered with" by a developer in a feature branch.

# 📦 Cross-Workflow Artifact Sharing

When using `workflow_run`, artifacts are the bridge between separate execution contexts.

## 🗝️ The "run-id" Logic
In a standard workflow, `download-artifact` looks for files in the **current** run. In a `workflow_run` child, you must point it to the **Parent's ID**:
* **Variable:** `${{ github.event.workflow_run.id }}`
* **Requirement:** The Parent must have successfully finished the `upload-artifact` step before the Child tries to download.

## ⚖️ Retention & Storage
* **Optimization:** Use `retention-days: 1` for reports that are only needed for the downstream compliance check. This keeps your GitHub Storage usage low.
* **Security:** Artifacts are unencrypted zip files. Do not store raw secrets or private keys in artifacts.

## 🔄 The "Downstream" Advantage
The Compliance workflow can now fail **without** failing the main Build. This is useful for "Soft Gates" where you want to notify security teams but not block the developer's immediate feedback loop.

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

### GitHub CLI vs. Web UI: The Admin Cheat Sheet

| Feature | GitHub CLI (`gh`) | Web UI (Browser) |
| :--- | :--- | :--- |
| **Workflow Dispatch** | Defaults to your **current local branch** automatically. | Requires manual selection from a dropdown menu. |
| **Log Monitoring** | `gh run watch` streams live interactive logs to your terminal. | Static page that requires browser refreshes or waiting for the UI to update. |
| **Secret Management** | `gh secret set -f .env` allows for **bulk uploads** from files. | Requires manual "New Secret" entry for every individual key-value pair. |
| **Repo Creation** | One command to create remote, add origin, and push: `gh repo create --push`. | Multi-step process involving browser clicks and manual `git` commands in the terminal. |
| **Auth Verification** | `gh auth status` instantly lists your token's active scopes and permissions. | Requires navigating through several layers of Personal Settings to find the PAT. |
| **Error Handling** | Provides machine-readable JSON output for scripting (`--json`). | Visual-only; requires manual copying/pasting for documentation or scripts. |

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

# Starter Workflows
    
Starter Workflows allow Organization Admins to provide "Company Approved" templates that appear when a developer clicks **Actions > New Workflow**.

## 🏗️ 1. The Magic Repository: `.github`
To enable Starter Workflows for your entire Organization (or account), you must create a public repository named exactly `.github`.

* **Location:** `https://github.com/<your-org>/.github`
* **Path for Workflows:** `.github/workflow-templates/`

## 📂 2. Directory Structure
Inside the `.github` repo, you need three files for every template you want to offer:

```text
.github/
└── workflow-templates/
    ├── node-ci.yml           # The actual YAML code
    ├── node-ci.properties.json # Metadata (Name, Description, Icon)
    └── node-ci.svg            # Optional: Custom icon