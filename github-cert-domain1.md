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