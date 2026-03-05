# GH-200 Study Guide: Domain 1 Checklist (with Code Anchors)

## 1. Author and Manage Workflows (20–25%)

### Configure workflow triggers and events
- [x] **Configure workflows to run for scheduled, manual, webhook, and repository events** (`on: schedule: - cron:`, `on: workflow_dispatch:`, `on: push:`, `on: pull_request:`)

[events that triggered workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows)

> The exam will test your ability to choose the right trigger for the right scenario: 
> * Scheduled (schedule): Best for nightly builds or security scans (uses cron syntax).
> * Manual (workflow_dispatch): Best for testing or "one-off" administrative tasks.
> * Webhook (repository_dispatch): Best when an external system needs to start a process in GitHub.
> * Repository Events: Triggers based on internal GitHub actions like push, pull_request, release, or label.

> Notes:
> * For repository_dispatch to work, the request must be sent to the default branch (usually main). If you try to trigger a webhook event on a feature branch, it won't fire unless that workflow file also exists on the default branch.
> * If a question asks if a cron job is "real-time precise," the answer is no.
> * Default Branch Requirement: Similar to webhooks, the workflow file must exist on the default branch (usually main) for the schedule to trigger.

- [x] **Choose appropriate scope, permissions, and events for workflow automation** (`permissions: contents: read`, `pull_request` vs `pull_request_target`)

> The GITHUB_TOKEN permissions can be changes to be more restrictive by an admin

> Also can be grant or deny granually, this is by adding the "permissions" tag on the workflow. All the permission define here will be granted, and all not mention will be denied. The permissions can be added globally, and again in a step or job level.

> If you move your workflow to a different organization that has the "Restricted" default, your workflow will break unless you have explicitly requested the permissions it needs.

>GITHUB_TOKEN permissions can only be the same or more restrictive in nested workflows. For example, in the workflow chain A > B > C, if workflow A has package: read token permission, then B and C cannot have package: write permission. For more information, see Use GITHUB_TOKEN for authentication in workflows.

> When a reusable workflow is triggered by a caller workflow, the github context is always associated with the caller workflow. The called workflow is automatically granted access to github.token and secrets.GITHUB_TOKEN. For more information about the github context, see


### GitHub Actions Cron Syntax: The Schedule Cheat Sheet

GitHub uses a five-field format (UTC time) to schedule workflows.

```text
┌───────────── minute (0 - 59)
│ ┌─────────── hour (0 - 23)
│ │ ┌───────── day of the month (1 - 31)
│ │ │ ┌─────── month (1 - 12 or JAN-DEC)
│ │ │ │ ┌───── day of the week (0 - 6 or SUN-SAT)
│ │ │ │ │
* * * * *
```

| Frequency | Cron Expression | Notes |
| :--- | :--- | :--- |
| **Every 15 Minutes** | `*/15 * * * *` | Great for frequent polling or small checks. |
| **Daily at Midnight** | `0 0 * * *` | Standard for nightly builds or cleanups. |
| **Weekly (Sundays)** | `0 0 * * 0` | Use `SUN` or `0` for Sunday. |
| **Business Hours** | `0 9 * * 1-5` | Runs at 9:00 AM UTC, Monday through Friday. |
| **Quarterly** | `0 0 1 */3 *` | Runs at midnight on the 1st of every 3rd month. |

---

### 🎓 GH-200 Exam "Watch-Outs" (Domain 1.2)

* **UTC Only**: All schedules are evaluated in UTC; you must manually calculate offsets for local time zones.
* **Best Effort**: Scheduled workflows can be delayed during peak GitHub load; they are not real-time precise.
* **Default Branch**: The workflow file MUST exist on the default branch (usually `main`) for the cron to trigger.

```sh
# This sets permissions for EVERY job in the workflow
permissions:
  contents: write    # Allow pushing code/creating tags
  pull-requests: read # Allow reading PR data
  issues: write      # Allow commenting on issues


$gh auth status                                                                                                               
github.com
  ✓ Logged in to github.com account C11R11 (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token: gho_************************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'
```

- [?] **Define and validate workflow_dispatch inputs (types, required, defaults) and pass inputs to reusable workflows via workflow_call with inputs and secrets mapping** (`inputs: name: type: string, default: 'val'`, `with: arg: ${{ inputs.name }}`, `secrets: inherit`)


| Host Repository Visibility | Accessibility Criteria |
| :--- | :--- |
| **Same Repository** | Accessible by default. |
| **Public** | Accessible if the enterprise allows the use of public reusable workflows. |
| **Internal** | Accessible if repository settings are configured to allow enterprise sharing. |
| **Private** | Accessible if repository settings are configured to allow specific access. |


**PRUEBAS DE INHERIT**
* NUEVO REPO EXTERNO A ORGANIZACIÓN
* NUEVO REPO EN LA ORGANIZACIÓN

> GITHUB_TOKEN: Github token created automatically for be used on a workflow run. The token is only for the repo that contains the workflow, and expires when the workflows ends. Can be called with {{secrets.GITHUB_TOKEN}} or {{github.token}}. Can be passed in the with for an action, or in a env in a step. It's more secure to use this token instead of a fine-graned or classic PAT, because it expires.

[how to use the GITHUB_TOKEN to authenticate on behalf of GitHub Actions.](https://docs.github.com/en/actions/tutorials/authenticate-with-github_token#permissions-for-the-github_token)

### Design and implement workflow structure
- [x] **Use jobs, steps, and conditional logic** (`jobs:`, `steps:`, `if: github.event_name == 'push'`)

- [x] **Implement dependencies between jobs** (`needs: [job_a, job_b]`)

- [x] **Use workflow commands and environment variables** (`echo "VAR=VAL" >> $GITHUB_ENV`, `::error::message`)

> env variables can be accessed like a context value with ${{ env.PROPERTY }}. The context will be interpolated and replaced by a string before the job is sent to a runner. This can be used in a bash script or command. 

> THE UPDATED VALUE CANNOT BE SEEN BY ANOTHER JOB, ONLY THE JOB THAT DID THE ENV MODIFICATION.

> However, you cannot use runner environment variables in parts of a workflow that are processed by GitHub Actions and are not sent to the runner. Instead, you must use contexts.

```sh
The run: echo "TODAY=$(date +%Y-%m-%d)" >> $GITHUB_ENV generates a new or update a environment variable, and this can be used on all the job
```

[Variables reference](https://docs.github.com/en/actions/reference/workflows-and-actions/variables)
[About workflow commands](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#about-workflow-commands)
[Setting an error message](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#setting-an-error-message)
[About workflow commands](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands)
[Environment files](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#environment-files)

- [x] **Use service containers (services:) for dependent services (databases, queues); configure ports, health checks, and container options** (`services: redis: image: redis`, `ports: ["6379:6379"]`, `options: "--health-cmd ..."`)

> Container services are for a single job (fresh container for job and then gets eliminated when the job ends).
> Every job must define their own service 

| Feature      | Job Container (`container:`)                                  | Service Container (`services:`)                                      |
|--------------|---------------------------------------------------------------|-----------------------------------------------------------------------|
| Role         | The "Home" where your steps execute.                         | The "Sidecar" that provides a dependency (DB, Cache).                |
| Usage        | Replaces the runner's default OS environment.                | Runs alongside your code in a shared network.                        |
| Networking   | Steps run inside this container.                             | Steps talk to it via a hostname (the service label).                 |

```sh
jobs:
  test-database:
    runs-on: ubuntu-latest
    # This job runs inside a Node container
    container: node:20 
    
    # These services are sidecars to the job
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: password
      redis:
        image: redis

    steps:
      - name: Check Connectivity
        # Because we are in a 'job container', we use the service label as the host
        run: |
          ping -c 1 postgres
          ping -c 1 redis
```

| If your Job uses...        | Access Service via... | Do you need `ports:`? | Why? | Key Exam Memory Trigger |
|----------------------------|-----------------------|------------------------|------|--------------------------|
| runs-on: ubuntu-latest     | localhost:<port>      | ✅ Yes                 | The runner maps the service container port to the host’s loopback interface. | No job container → you must expose ports to reach the service. |
| container: node:20         | <service_label>:<port>| ❌ No                  | Both containers are on the same user-defined Docker bridge network; DNS resolves the service label automatically. | Job container + service container → same Docker network → use label as hostname. |

- [x] **Implement YAML anchors and aliases (&, * and merge <<) to reuse repeated mappings/steps within a single workflow file** (`env: &defaults`, `env: *defaults`, `<<: *defaults`)

> You can use YAML anchors and aliases to reduce repetition in your workflows. An anchor (marked with &) identifies a piece of content that you want to reuse, while an alias (marked with *) repeats that content in another location.

### 🔄 Reusability: Anchors vs. Composite Actions

| Capability | YAML Anchors | Composite Actions |
| :--- | :--- | :--- |
| **Portability** | Internal to one `.yml` file only. | Can be stored in a central repo and used by many. |
| **Flexibility** | Static; cannot change based on context. | Dynamic; uses `inputs` to change behavior. |
| **Step Injection** | ❌ Cannot add extra steps to an anchored list. | ✅ Can be used alongside other steps in a job. |
| **Secret Access** | Direct (since it's in the same file). | Must be passed explicitly via `inputs`. |

#### 💡 When to use which?
> Use **Anchors** when you have 5 jobs in ONE file that all use the exact same `env` or `strategy.matrix`.
> Use **Composite Actions** when you want to standardize a "Build and Lint" process for every repo in your Organization.

- [x] **Use strategy and matrix to generate job variations (OS, language/runtime versions); apply include/exclude; control fail-fast and max-parallel; optimize matrix size for cost and performance; account for runner image changes (Ubuntu 20.04 deprecation, Windows Server 2025 migration for windows-latest)** (`strategy: matrix: node: [18, 20]`, `include:`, `exclude:`, `fail-fast: false`, `runs-on: windows-2025`)

[Strategy Context Reference](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idstrategy)

[Running variations of jobs in a workflow](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/run-job-variations)

### 🧬 GitHub Actions Matrix Strategy Reference

The `strategy` block allows you to run a single job definition across multiple variations simultaneously.

| Property | Purpose | Exam Focus |
| :--- | :--- | :--- |
| **`matrix`** | Defines the variables and their values. | Every combination (Cartesian product) is created by default. |
| **`fail-fast`** | Determines if GitHub should cancel all in-progress jobs if one fails. | Default is `true`. Set to `false` for exhaustive testing. |
| **`max-parallel`** | Limits the number of jobs running at the same time. | Use this to avoid hitting API rate limits or runner quotas. |
| **`include`** | Adds specific combinations or extra variables to a matrix. | Can add a unique variable (like a specific `flag`) to just one row. |
| **`exclude`** | Removes specific combinations from the matrix. | Essential for removing unsupported versions (e.g., Node 20 on Windows 2019). |

| Feature | Syntax | Impact |
| :--- | :--- | :--- |
| **Expansion** | `matrix: { os: [A, B], node: [1, 2] }` | Creates 4 jobs (Ax1, Ax2, Bx1, Bx2). |
| **Include** | `include: [{ os: A, flag: '--fast' }]` | Adds `matrix.flag` ONLY to Ax1 and Ax2. |
| **Exclude** | `exclude: [{ os: B, node: 1 }]` | Removes the Bx1 job from the run. |
| **Fail-Fast** | `fail-fast: false` | Forces all 4 jobs to finish even if 3 fail. |

---

> The matrix context is only available inside a job that has a strategy: matrix: defined. You cannot use ${{ matrix.anything }} in a different job unless that job also has its own matrix.

> If you use ${{ matrix.os }} in the runs-on field, GitHub will "multiply" that job. If your matrix has 2 OSs and 3 Versions, GitHub creates 6 distinct jobs.

### 🧬 Matrix Variable Naming Rules

| Component | Rule | Example |
| :--- | :--- | :--- |
| **Context Name** | **Reserved.** Must be `matrix`. | `${{ matrix.version }}` |
| **Property Keys** | **Custom.** Use any alphanumeric name. | `os:`, `runtime:`, `database:` |
| **Property Values** | **Arrays.** Must be a list of values. | `[18, 20]` or `['latest', 'beta']` |
| **Accessing Values** | **Expression Syntax.** Use `${{ }}`. | `runs-on: ${{ matrix.os }}` |

#### ⚠️ Warning: Reserved Key Names
Avoid using keys that match top-level YAML properties like `include` or `exclude` as your variable names, as this will confuse the YAML parser.
* **Bad:** `matrix: { include: [v1, v2] }`
* **Good:** `matrix: { version: [v1, v2] }`

### 🚨 Critical Update: Runner Image Lifecycles

As a DevOps Engineer, you must track image deprecations to prevent "Pipeline Rot".

* **Ubuntu-latest**: Transitions from **22.04** to **24.04** periodically. Be aware that `ubuntu-20.04` is reaching End-of-Life.
* **Windows-latest**: In 2026, this typically points to **Windows Server 2025**. If your code relies on older drivers, you must pin to `windows-2022`.
* **The "Pinned" Rule**: For production reliability, always prefer `ubuntu-22.04` over `ubuntu-latest` to avoid surprise breaks during image migrations.

- [x] **Use predefined contexts (github, runner, env, vars, secrets, inputs, matrix, needs, strategy, job, steps, github.event, github.ref) to access workflow, repository, and runtime metadata; understand immutable actions behavior and version pinning requirements** (`${{ github.sha }}`, `${{ secrets.KEY }}`, `uses: actions/checkout@v4` vs `@SHA`)

### 🔑 Metadata Access & Version Pinning

#### 1. Immutable Actions & Versioning
The exam will test your understanding of "Risk vs. Stability" regarding how you call actions.

| Versioning Method | Syntax | Risk Level | Why? |
| :--- | :--- | :--- | :--- |
| **Commit SHA** | `@a456...` | **Safest** | Immutable. Prevents "supply chain attacks" if a tag is moved. |
| **Full Tag** | `@v4.1.1` | **Stable** | Refers to a specific release, but tags *can* be deleted/moved. |
| **Major Tag** | `@v4` | **Standard** | Gets bug fixes automatically, but risk of breaking changes. |
| **Branch Name** | `@main` | **Unsafe** | Code can change at any second; builds will be inconsistent. |

#### 2. Predefined Contexts Essentials

- **`${{ github.sha }}`**: The specific commit ID that triggered the workflow. Use this for unique image tagging.
- **`${{ github.ref }}`**: The branch or tag that triggered the run (e.g., `refs/heads/main`).
- **`${{ secrets.GITHUB_TOKEN }}`**: Automatically generated for every job. It expires when the job finishes.
- **`${{ runner.temp }}`**: A temporary directory on the runner that is cleaned up after every job.

> If a question asks how to **strictly** meet security compliance for a regulated industry (like Banking), the answer is always **Pin actions to a full SHA**. GitHub recommends using the SHA and adding a comment with the version number for readability:

> Pinning an action to a full-length commit SHA is currently the only way to use an action as an immutable release. Pinning to a particular SHA helps mitigate the risk of a bad actor adding a backdoor to the action's repository, as they would need to generate a SHA-1 collision for a valid Git object payload. When selecting a SHA, you should verify it is from the action's repository and not a repository fork.

> To force to pin action to a commit SHA is on Organization->Actions->General-> Require actions to be pinned to a full-length commit SHA

- [x] **Evaluate expressions with ${{ }} referencing contexts; distinguish static (workflow parse) vs runtime evaluation; prevent secret leakage in logs and expressions** (`if: ${{ github.event_name == 'push' }}`, `env: PASSWORD: ${{ secrets.PW }}`)

[Syntax reference](https://docs.github.com/en/actions/learn-github-actions/expressions)
[Context Availability](ttps://docs.github.com/en/actions/learn-github-actions/contexts%23context-availability)
[Security Hardening (Secrets)](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions%23using-secrets)

#### 1. Static vs. Runtime Evaluation
Understanding *when* an expression is evaluated is a common GH-200 trick.

| Type | When it Happens | Where it's Used | Example |
| :--- | :--- | :--- | :--- |
| **Static (Parse-time)** | Before the job starts, by GitHub's central server. | `if:`, `strategy:`, `concurrency:`, `env:` (job/wf level). | `${{ github.event_name == 'push' }}` |
| **Runtime** | While the script is running inside the runner's shell. | Inside `run:` scripts or shell environment. | `$MY_ENV_VAR` or `${{ steps.id.outputs.val }}` |

**⚠️ The Golden Rule:** Use `${{ }}` for GitHub-level logic (skipping jobs/steps). Use shell variables (like `$VAR`) for script-level logic.

---

#### 2. Expression Rules
- **Case Insensitivity**: Context keys are case-insensitive (e.g., `GITHUB.REF` == `github.ref`).
- **Implicit Strings**: Inside `if:` blocks, you don't *need* the `${{ }}` wrapper, but it is best practice to include it for consistency.
- **Short-Circuiting**: `(false && github.sha)` will stop at `false` and never evaluate the second half.

---

#### 3. Preventing Secret Leakage
GitHub masks secrets in the logs, but it isn't foolproof.

| Risk | Mitigation Strategy |
| :--- | :--- |
| **Log Leak** | **NEVER** `echo` a secret directly. Use it as an environment variable instead: `env: KEY: ${{ secrets.KEY }}`. |
| **Masking Bypass** | Base64 encoding or JSON-structured secrets often bypass the "Exact Match" masker. **Don't store JSON blobs as secrets**. |
| **Dynamic Masks** | If you generate a sensitive value during a run, use the workflow command: `echo "::add-mask::$VALUE"`. |
| **Context Leaks** | Avoid dumping the entire `github` context (e.g., `toJson(github)`) if your event payload might contain tokens or emails. |

---

#### 🎓 GH-200 Exam Scenarios
* **Scenario:** "A job should only run on the main branch for pushes."
  * **Answer:** `if: github.event_name == 'push' && github.ref == 'refs/heads/main'`.
* **Scenario:** "How do you ensure a dynamic API token generated in Step 1 is hidden in logs for Step 2?"
  * **Answer:** Use `::add-mask::` on the token in Step 1.

- [ ] **Leverage editor tooling (GitHub Actions VS Code extension / YAML schema completion, metadata IntelliSense, validation) to author and maintain workflows efficiently** (Action/YAML linting extension in VS Code)

🌐 GitHub Actions Contexts Quick Reference

| Context | Purpose | Common Properties |
| :--- | :--- | :--- |
| **`github`** | Information about the workflow run and the event that triggered it. | `github.ref`, `github.repository`, `github.actor`, `github.event` |
| **`env`** | Variables defined at the workflow, job, or step level using the `env:` key. | `env.MY_VAR` |
| **`vars`** | Configuration variables defined at the Org, Repo, or Environment level. | `vars.API_URL`, `vars.PORT` |
| **`secrets`** | Sensitive data (tokens, passwords) defined in GitHub Settings. | `secrets.GITHUB_TOKEN`, `secrets.DB_PASSWORD` |
| **`inputs`** | Data passed to a `workflow_dispatch` or `workflow_call` event. | `inputs.deploy_target`, `inputs.debug_mode` |
| **`matrix`** | The current job's specific combination of variables in a matrix strategy. | `matrix.os`, `matrix.node-version` |
| **`needs`** | Outputs and status from jobs that this job depends on (via `needs:`). | `needs.build.outputs.version`, `needs.build.result` |
| **`runner`** | Information about the machine executing the current job. | `runner.os`, `runner.temp`, `runner.tool_cache` |
| **`job`** | Metadata about the current job being executed. | `job.status`, `job.container.id` |
| **`steps`** | Status and outputs from steps already executed in the current job. | `steps.step_id.outputs.my_output`, `steps.step_id.outcome` |
| **`strategy`** | Information about the matrix execution strategy. | `strategy.job-index`, `strategy.job-total` |

**Github Context**

[Contexts reference](https://docs.github.com/en/actions/reference/workflows-and-actions/contexts#available-contexts)

[github context](https://docs.github.com/en/actions/reference/workflows-and-actions/contexts#github-context)

[events that triggered workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows)

```text
# Quick info
github.ref -> branch (refs/heads/main)
github.repository -> the owner/repo (cert-labs-hq/automations-repo)
github.action -> the user who triggers the workflow (if it's a re-run the github.triggerent_action may be different)
github.event_name -> The name of the event that triggered the workflow run. (workflow_dispatch)
github.event -> The full event webhook payload.
github.event.workflow -> the workflow yml path (.github/workflows/test-version.yml)
github.workflow_ref -> full workflow path (cert-labs-hq/automations-repo/.github/workflows/test-version.yml@refs/heads/main)
```

### Manage workflow execution and outputs
- [ ] **Configure caching and artifact management; apply retention policies via REST APIs (logs, artifacts, workflow runs) at org/repo level** (`uses: actions/cache@v4`, `uses: actions/upload-artifact@v4`, `retention-days: 30`)
- [ ] **Pass data between jobs and steps (artifacts, outputs, environment files via GITHUB_ENV and GITHUB_OUTPUT, reusable workflow outputs)** (`echo "val=foo" >> $GITHUB_OUTPUT`, `outputs: my_out: ${{ steps.id.outputs.val }}`)
- [ ] **Generate job summaries using GITHUB_STEP_SUMMARY for rich Markdown reports (test results, coverage, links)** (`echo "### Test Passed" >> $GITHUB_STEP_SUMMARY`)
- [ ] **Add workflow status badges and environment protections** (`![Badge](https://github.com/...)`, `environment: production`)


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

### Implement security best practices
- [ ] **Use environment protections and approval gates** (`environment: production`, "Required reviewers", "Wait timer")
- [ ] **Identify and use trustworthy actions from the Marketplace** (Look for the "Verified Creator" blue badge ✅)
- [ ] **Mitigate script injection (sanitize/validate inputs, least-privilege permissions, avoid untrusted data in run:, proper shell quoting, prefer vetted actions over inline scripts)** (Use `env` variables for `${{ github.event... }}` instead of direct expansion in `run:`)
- [ ] **Understand GITHUB_TOKEN lifecycle (ephemeral, scoped), configure granular permissions, contrast with PAT; restrict write scopes** (`permissions: contents: read`, `pull-requests: write`)

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