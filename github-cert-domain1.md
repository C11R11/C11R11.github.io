[Github cert](github-cert.md)

# Author and Manage Workflows

[Variables reference](https://docs.github.com/en/actions/reference/workflows-and-actions/variables)
[About workflow commands](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#about-workflow-commands)
[Setting an error message](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#setting-an-error-message)
[About workflow commands](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands)
[Environment files](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#environment-files)

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

[On schedule reference](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#onschedule)

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

# Dispatch

| Host Repository Visibility | Accessibility Criteria |
| :--- | :--- |
| **Same Repository** | Accessible by default. |
| **Public** | Accessible if the enterprise allows the use of public reusable workflows. |
| **Internal** | Accessible if repository settings are configured to allow enterprise sharing. |
| **Private** | Accessible if repository settings are configured to allow specific access. |

# 🔐 GitHub Actions: Cross-Repo Secret & Tag Rules

## 1. Secret Inheritance (`secrets: inherit`)
* **Private/Internal Repos:** Works perfectly within the same Organization.
* **Public Repos:** **Blocked.** You must pass secrets explicitly using the `secrets:` key in the caller workflow.
* **Reason:** Prevents secret leakage via forks or malicious pull requests.

## 2. Remote Tagging Checklist
To call a workflow by tag (e.g., `@v1`), the following must be true:
1. The repository is **Public** (or Private with "Access" enabled).
2. The tag was pushed using `git push --tags`.
3. The reference uses the full path: `owner/repo/.github/workflows/workflow.yml@v1`.

## 🛠️ Best Practice for Public Libraries
When building a public automation library:
1. Keep the **Workflow** public.
2. Keep the **Secrets** in the repository settings of the *Caller* repo.
3. Use **Explicit Secrets** mapping in the YAML to maintain a clear security contract.

### ⚠️ Reusable Workflow Access Matrix

| Caller Repo | Called (Reusable) Repo | Result |
| :--- | :--- | :--- |
| **Public** | **Public** | ✅ Supported |
| **Private** | **Private** | ✅ Supported (Requires Settings > Actions > Access) |
| **Private** | **Public** | ✅ Supported |
| **Public** | **Private** | ❌ **FAILED (HTTP 422)** |

**Note:** For personal accounts/organizations on the Free tier, "Internal" visibility is not available. You only have Public or Private.

  > GITHUB_TOKEN: Github token created automatically for be used on a workflow run. The token is only for the repo that contains the workflow, and expires when the workflows ends. Can be called with {{secrets.GITHUB_TOKEN}} or {{github.token}}. Can be passed in the with for an action, or in a env in a step. It's more secure to use this token instead of a fine-graned or classic PAT, because it expires.

[how to use the GITHUB_TOKEN to authenticate on behalf of GitHub Actions.](https://docs.github.com/en/actions/tutorials/authenticate-with-github_token#permissions-for-the-github_token)

# Workflow commands

> env variables can be accessed like a context value with ${{ env.PROPERTY }}. The context will be interpolated and replaced by a string before the job is sent to a runner. This can be used in a bash script or command. 

> THE UPDATED VALUE CANNOT BE SEEN BY ANOTHER JOB, ONLY THE JOB THAT DID THE ENV MODIFICATION.

> However, you cannot use runner environment variables in parts of a workflow that are processed by GitHub Actions and are not sent to the runner. Instead, you must use contexts.

```sh
The run: echo "TODAY=$(date +%Y-%m-%d)" >> $GITHUB_ENV generates a new or update a environment variable, and this can be used on all the job
```

# Anchors and aliases

> You can use YAML anchors and aliases to reduce repetition in your workflows. An anchor (marked with &) identifies a piece of content that you want to reuse, while an alias (marked with *) repeats that content in another location.

```text
Anchors and aliases

There are 2 parts to this:
- The anchor '&' which defines a chunk of configuration
- The alias '*' used to refer to that chunk elsewhere
```

```yml
definitions: 
  steps:
    - step: &build-test
        name: Build and test
        script:
          - mvn package
        artifacts:
          - target/**
pipelines:
  branches:
    develop:
      - step: *build-test
    main:
...

# You can use overrides with the characters '<<:' to add more values, or override existing ones.
    main:
      - step: 
          <<: *build-test
          name: Testing on Main
```
> YAML anchors and aliases cannot contain the ' [ ', ' ] ', ' { ', ' } ', and ' , ' characters.

[yaml anchors and aliases reference](https://support.atlassian.com/bitbucket-cloud/docs/yaml-anchors/)


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

# Expressions

#### 1. Static vs. Runtime Evaluation
Understanding *when* an expression is evaluated is a common GH-200 trick.

| Type | When it Happens | Where it's Used | Example |
| :--- | :--- | :--- | :--- |
| **Static (Parse-time)** | Before the job starts, by GitHub's central server. | `if:`, `strategy:`, `concurrency:`, `env:` (job/wf level). | `${{ github.event_name == 'push' }}` |
| **Runtime** | While the script is running inside the runner's shell. | Inside `run:` scripts or shell environment. | `$MY_ENV_VAR` or `${{ steps.id.outputs.val }}` |

**⚠️ The Golden Rule:** Use `${{ }}` for GitHub-level logic (skipping jobs/steps). Use shell variables (like `$VAR`) for script-level logic.

> The Rule: You can only use the github, inputs, vars (Configuration Variables), and matrix contexts in the with block of a uses statement.

#### 2. Expression Rules
- **Case Insensitivity**: Context keys are case-insensitive (e.g., `GITHUB.REF` == `github.ref`).
- **Implicit Strings**: Inside `if:` blocks, you don't *need* the `${{ }}` wrapper, but it is best practice to include it for consistency.
- **Short-Circuiting**: `(false && github.sha)` will stop at `false` and never evaluate the second half.

# Tooling


To author workflows efficiently, you must move from "Writing YAML" to "Developing Workflows." This requires three layers of tooling.

## 1. The "Must-Have" VS Code Extension
**Extension Name:** [GitHub Actions](https://marketplace.visualstudio.com/items?itemName=GitHub.vscode-github-actions) (by GitHub)

### Key Features:
* **IntelliSense:** Autocomplete for `runs-on`, `with`, `outputs`, and even context variables like `${{ github.event... }}`.
* **Inline Documentation:** Hover over a keyword (like `strategy`) to see the official documentation and allowed values.
* **Workflow Management:** Start, stop, and view logs for your actions directly inside VS Code without switching to the browser.

---

## 2. YAML Schema Validation
GitHub Actions uses a specific **JSON Schema**. You can force VS Code to recognize your `.yml` files as GitHub Actions to get red squiggly lines when you make a syntax error.

### Settings.json Configuration:
```json
"yaml.schemas": {
  "[https://json.schemastore.org/github-workflow.json](https://json.schemastore.org/github-workflow.json)": ".github/workflows/*.{yml,yaml}",
  "[https://json.schemastore.org/github-action.json](https://json.schemastore.org/github-action.json)": "**/action.{yml,yaml}"
}
```
### 3.- Actionlint

Standard YAML linting only checks if the file is valid YAML. Actionlint checks if the logic is valid for GitHub Actions (e.g., "Does this secret exist?" or "Is this shell command valid?").

* Installation: 
```bash
brew install actionlint
```
* Usage: Run actionlint in your terminal to catch errors like:
* Invalid if expressions.
* Missing shell: on custom steps.
* Using ${{ }} inside a run: block incorrectly (Shell Injection risks

# 🗄️ Multi-Cache Coexistence

When a new cache is generated, the old one isn't immediately deleted. They coexist until an eviction rule triggers.

## 🕒 The Coexistence Period
* **Why keep the old one?** If you switch back to the previous branch or revert the `package.json` change, GitHub will find the old cache immediately. This is "Zero-Latency Reverts."
* **Cleanup:** GitHub handles the cleanup automatically. You don't need to manually delete the old cache unless you are hitting the 10GB repository limit.

## 🏁 Race Conditions
If two workflows run at the exact same time with the same new key:
* The first one to finish "wins" and saves the cache.
* The second one will simply fail to save (silently) because the key now exists. This prevents data corruption.

# QUIZ

1. You have a workflow that must only run after another specific workflow has successfully completed. Which trigger should you use?

workflow run

# 🔗 workflow_call vs. workflow_run

While both connect two workflow files, they serve completely different architectural purposes.

## 1. workflow_call (The "Reusable" Pattern)
This is used for **templates**. You create a "standard" workflow once and call it from many different repositories or files.

* **How it works:** The "Caller" (Parent) explicitly triggers the "Called" (Child) workflow.
* **Context:** The Child runs as if it were part of the Parent. It shares the same `GITHUB_RUN_ID`.
* **Use Case:** A standard "Security Scan" workflow that every team in the company must use.

**Example (Child Workflow):**
```yaml
on:
  workflow_call:
    inputs:
      username:
        required: true
        type: string
```

## 2.- workflow_run (The Reactive Trigger)

The `workflow_run` trigger allows a workflow to execute based on the **completion** or **status** of a separate workflow file in the same repository.

## ⚙️ How it works
Unlike `workflow_call` (where the parent calls the child), `workflow_run` is a **downstream listener**. The "Child" workflow sits and waits for the "Parent" workflow to finish.

### Basic Syntax
```yaml
on:
  workflow_run:
    workflows: ["CI Pipeline"] # The 'name' field of the parent workflow
    types:
      - completed
      - requested
    branches:
      - main
      - 'releases/**'
```


2. You are implementing Infrastructure as Code with Terraform. You want to ensure that if a second 'Apply' workflow starts while one is already running, the new one is cancelled to avoid state locks. Which keyword should you use?

concurrency

## Concurrency: Preventing Overlaps
Used to ensure only one run of a specific group executes at a time. Critical for deployments (e.g., Terraform, Database Migrations).

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true # Drops the old run if a new one starts
```

3. You need to run your test suite across three different versions of Node.js (18, 20, 22). What is the most efficient way to define this in Domain 1?

Use a matrix strategy wihtin a single job

4. A developer wants to trigger a workflow only when changes are made to the 'docs/' folder on the 'main' branch. How should the trigger be configured?

on: push: branches: [main] paths['docs/**']

# Github Containers (jobs and services)

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

## GitHub Actions Contexts Quick Reference

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
# 📊 Data Management: Artifacts vs. Cache vs. Outputs

In GitHub Actions, managing data efficiently depends on understanding where the data needs to go and how long it needs to live.

| Feature | Scope | Persistence | Best Use Case |
| :--- | :--- | :--- | :--- |
| **Outputs** | **Job-to-Job** | Current Run Only | Passing small strings, IDs, version numbers, or boolean flags between dependent jobs. |
| **Artifacts** | **Workflow-to-User** | Up to 90 Days | Storing build binaries (.exe, .apk), test coverage reports, or logs for human download. |
| **Cache** | **Run-to-Run** | Up to 7 Days* | Speeding up builds by reusing expensive dependencies like `node_modules`, `~/.nuget/packages`, or `~/.m2`. |

## 🔑 Key Strategic Differences

### 1. Direction of Flow
* **Outputs:** Move **forward** in time within a single workflow execution (Job A → Job B).
* **Artifacts:** Move **outward** to the user or an external storage area.
* **Cache:** Moves **backward** from a past successful run into a future run.

### 2. Storage & Costs
* **Artifacts:** Count against your GitHub storage quota (once the limit is reached, you are billed).
* **Cache:** GitHub evicts the oldest cache once you hit a **10GB limit per repository**. It does not cost extra money but is "best effort" storage.

### 3. The "Bust" Mechanism
* **Outputs/Artifacts:** These are fresh for every run; no "busting" required.
* **Cache:** Requires a change in the `key` to invalidate. If your `package-lock.json` or `csproj` hasn't changed, the cache will persist. 

---
*Note: Caches are automatically deleted if they haven't been accessed in 7 days.*

## Environment variables




```sh
echo "{environment_variable_name}={value}" >> "$GITHUB_ENV"
```


```text
To avoid issues, it's good practice to treat environment variables as case sensitive, irrespective of 
the behavior of the operating system and shell you are using.
```



## Environment files

During the execution of a workflow, the runner generates temporary files that can be used to perform certain actions.

[](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-commands#environment-files)


# QUIZ FAILS

1. In a private repository, why are workflow badges not accessible externally

A: to prevent external embedding or linking from unauthorized sources

[https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)

2. What is the minimum time granularity available for scheduling GitHub Actions?

A: five minutes

> The shortest interval you can run scheduled workflows is once every 5 minutes.

[reference](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onschedule:~:text=The%20shortest%20interval%20you%20can%20run%20scheduled%20workflows%20is%20once%20every%205%20minutes.)

6. When might it be appropriate to use a combination of GitHub-hosted and self-hosted runners in a workflow?

when dealing with resource-intensive tasks

```text
Explanation
Using a combination of GitHub-hosted and self-hosted runners can be beneficial when dealing with resource-intensive tasks. GitHub-hosted runners may have limitations in terms of resources, so adding self-hosted runners with higher capabilities can help handle tasks that require more processing power or memory.
```

[https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)
[https://docs.github.com/en/actions/hosting-your-own-run](https://docs.github.com/en/actions/hosting-your-own-run)

7. Which default environment variables cannot be overwritten using the GITHUB_ENV file in a workflow?

A: GITHUB_* and RUNNER_*

```text
The default environment variables starting with "GITHUB_" and "RUNNER_" cannot be overwritten using
the GITHUB_ENV file in a workflow. These variables are reserved and cannot be modified to maintain 
the integrity and security of GitHub Actions workflows.
```
[https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#setting-an-environment-variable](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#setting-an-environment-variable)

8. Which of the following statements accurately describes the syntax rules for indentation in YAML used for defining workflow jobs in GitHub Actions?

A: YAML allows significant newlines and indentation, similar to Python, but unlike Python, it prohibits the use of literal tab characters for indentation

```text
YAML syntax for defining workflow jobs in GitHub Actions allows significant newlines and indentation, 
similar to Python. However, unlike Python, YAML prohibits the use of literal tab characters for indentation. 
This rule ensures consistency and readability in YAML files.
```
[https://learnxinyminutes.com/docs/yaml/](https://learnxinyminutes.com/docs/yaml/)

9. How can you specify dependencies between jobs in a workflow?

A: by defining dependencies in the workflow YAML file

10. What action should be taken if you want to find the expiration date of a specific artifact?

execute a specific API call to retrieve the expiration date

```text
To find the expiration date of a specific artifact in GitHub Actions, you need to execute a specific API call that retrieves the expiration date information. This API call will provide you with the necessary details about when the artifact will expire, allowing you to manage it effectively.
```

11. What is the recommended practice for treating environment variables in GitHub Actions, regardless of the operating system and shell used?

A: treat environment variables as case-sensitive

12. What is the primary purpose of using workflow commands as a run step in a GitHub Actions workflow?

A: to communicate instructions and information to the runner environment

13. What is the recommended approach for storing secrets larger than 48 KB?

use a workaround involving encryption with GPG and storing the decryption passphrase as a secret

```text

Using a workaround involving encryption with GPG and storing the decryption passphrase as a secret is the recommended 
approach for storing secrets larger than 48 KB. This method allows you to securely store and access large secrets while 
maintaining the necessary security measures.

To use secrets that are larger than 48 KB, you can use a workaround to store secrets in your repository 
and save the decryption passphrase as a secret on GitHub. For example, you can use gpg to encrypt a 
file containing your secret locally before checking the encrypted file in to your repository on GitHub
```

[Storing large secrets](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets#limits-for-secrets)


14. What is the purpose of the timeout-minutes keyword in a step?

A: it defines the time interval for individual commands within a step

```text
The timeout-minutes keyword is not related to defining the time interval for individual commands 
within a step. It specifically controls the maximum duration for the entire step to run, rather than 
setting time intervals for specific commands.
```

> The maximum number of minutes to let a job run before GitHub automatically cancels it. Default: 360

[jobs.<job_id>.timeout-minutes](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idtimeout-minutes)

15. In which scenario is using GitHub-hosted runners more suitable?

```text
when you leverage the resources provided by GitHub for continuous integration
```

16. What is the purpose of the jobs.<job_id>.runs-on configuration in a GitHub Actions workflow?

A: specifies the operating system and virtual environment for job execution

```text
Why the "Virtual Environment" is the "Right" Answer

The Machine: This refers to the hardware (CPU, RAM, OS).
The Virtual Environment: This refers to the software image (the "Virtual Image") that is loaded onto that machine.
```

[jobs.<job_id>.runs-on](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idruns-on)

17. What distinguishes YAML syntax from JSON when configuring workflow jobs in GitHub Actions?

A: YAML allows significant newlines and indentation

```text
YAML syntax distinguishes itself from JSON by allowing significant newlines and indentation. 
This feature makes YAML more human-readable and easier to work with, especially when configuring
workflow jobs in GitHub Actions where proper indentation is crucial for defining the structure of the workflow.
```

18. Which statement accurately describes the accessibility of default environment variables?

A: default environment variables are set by GitHub are are available at every step in a workflow

> The env context only contains variables you defined in the YAML.