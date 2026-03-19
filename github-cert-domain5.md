[Github cert](github-cert.md)

# Environments

> Environment Protection Rules (like Required Reviewers and Wait Timers) are a GitHub Enterprise and GitHub Pro/Team feature.

```text
The Exam Scenario: The exam might ask, "You have a developer using a personal fork to test a deployment. Why are the Environment Protection Rules not triggering?"

The Answer: Because the rules are defined in the Organization's environment, and the personal fork does not inherit the Org's environment settings.
```

## Source governance

Even though you won't configure environments inside the external repo, you use it to test Open Source Governance:

* Collaborator Permissions: How do you stop a contributor from the ExternalRepo from running a malicious workflow on your Org's runners?

* Fork PR Policy: In Org Settings -> Actions -> General, there is a setting called "Fork pull request workflows from outside collaborators."

  * In your lab, you should set this to "Require approval for all outside collaborators."

  * The Test: Try to open a PR from ExternalRepo to test-repo. You will see a yellow bar saying "Approval required to run workflows." This is a massive part of Domain 5 compliance.

## Environment Gates

### The Environment as a "Security Container"
In GitHub, an Environment is not just a name (like production or staging). It is a rule-based engine. When a workflow job says environment: production, GitHub pauses the runner and checks three specific "Gates" before it allows a single line of code to execute.

### The Three Pillars of Protection
A. Deployment Protection Rules (The "Human Gate")
* Required Reviewers: This is the most common gate. You define a list of people (or a Team) who must manually click "Approve" in the GitHub UI.

* Wait Timer: You can force a delay (e.g., 10 minutes) after a job is triggered. This is used in "Canary" deployments to ensure no immediate smoke-test failures occur before moving to the next stage.

B. Deployment Branch Policy (The "Source Gate")
* This is a critical compliance feature. It ensures that only code from specific branches (e.g., main or releases/*) can deploy to that environment.

* The Scenario: A developer creates a "Feature" branch and tries to trigger a production deploy. Even if they have the secret, the Environment will reject the job because it didn't come from main.

C. Environment Secrets & Variables (The "Access Gate")
* Instead of putting your AWS keys at the Repository level (where any workflow can use them), you put them at the Environment level.

* The Security Win: These secrets are only "unlocked" and injected into the runner after the reviewer clicks "Approve."

### How this fits your "Platform Engineer" Role
Using your infra-repo to manage these via Terraform is the "Gold Standard." It allows you to:

* Standardize: Every repo you give to your 10 devs automatically gets a production environment with the same rules.

* Audit: You have a version-controlled history of who is allowed to approve deployments.

* Prevent "Drift": If a developer tries to manually remove themselves as a reviewer in the UI, your Terraform plan will catch it and put the protection back.

## Order of operations

```sh
Workflow Start -> Environment Check -> Wait Timer (if any) -> Reviewer Approval -> Secret Injection -> Job Execution.
````
> If the Reviewer rejects the job, the Secrets are never decrypted or sent to the runner.

# Secrets

[Security Hardening (Secrets)](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions%23using-secrets)

## Preventing Secret Leakage
GitHub masks secrets in the logs, but it isn't foolproof.

| Risk | Mitigation Strategy |
| :--- | :--- |
| **Log Leak** | **NEVER** `echo` a secret directly. Use it as an environment variable instead: `env: KEY: ${{ secrets.KEY }}`. |
| **Masking Bypass** | Base64 encoding or JSON-structured secrets often bypass the "Exact Match" masker. **Don't store JSON blobs as secrets**. |
| **Dynamic Masks** | If you generate a sensitive value during a run, use the workflow command: `echo "::add-mask::$VALUE"`. |
| **Context Leaks** | Avoid dumping the entire `github` context (e.g., `toJson(github)`) if your event payload might contain tokens or emails. |


### 🧠 Key Concepts for GH-200 (Domain 5.1)

#### 1. The Masking Rule
GitHub replaces any secret referenced in the workflow with `***` in the logs. This is a **pattern-matching filter**, not a security sandbox.

#### 2. Manual Masking (`add-mask`)
When your script generates a sensitive value at runtime, you must register it with the masker so subsequent steps don't leak it:
```bash
# Registering a dynamic variable for masking
echo "::add-mask::$DYNAMIC_TOKEN"
```

---

#### 🎓 GH-200 Exam Scenarios
* **Scenario:** "A job should only run on the main branch for pushes."
  * **Answer:** `if: github.event_name == 'push' && github.ref == 'refs/heads/main'`.
* **Scenario:** "How do you ensure a dynamic API token generated in Step 1 is hidden in logs for Step 2?"
  * **Answer:** Use `::add-mask::` on the token in Step 1.

## 🛡️ GitHub Actions: Secret Security & Masking Cheat Sheet

| Risk Category | How it Leaks | Mitigation Strategy (Best Practice) |
| :--- | :--- | :--- |
| **Direct Logging** | Using `echo ${{ secrets.PWD }}` in a run script. | **NEVER** echo secrets. Map them to Environment Variables (`env:`) instead. |
| **Transformations** | Base64, Hex, or URL encoding the secret. | The masker only detects **exact matches**. Avoid encoding secrets in logs. |
| **Short Secrets** | Using common words (e.g., `test`) as a secret. | Use high-entropy strings (minimum 16+ characters). |
| **Generated Values** | Creating a dynamic token (e.g., AWS STS) during a run. | Use the workflow command: `echo "::add-mask::$VALUE"`. |
| **Structured Data** | Storing a JSON blob as a single secret. | Store values as individual secrets; parsing JSON risks exposing partial strings. |
| **Verbose Tools** | Using `curl -v` or `--debug` flags in CLI tools. | Disable debug mode in production to prevent header/payload leaks. |


# 🛡️ The Trust Circle: Why some refs are forced to SHA

In a professional DevOps environment, security levels are often applied based on the "Origin" of the code.

| Origin | Trust Level | Common Policy |
| :--- | :--- | :--- |
| **Official Actions** | Medium | **Forced SHA.** (e.g., `actions/checkout`). Prevents global supply chain attacks. |
| **Marketplace** | Low | **Forced SHA.** High risk of "Brand-jacking." |
| **Org/Internal** | High | **Tags/Branches Allowed.** Assumes internal peer review and access control. |

## 🛠️ The Release Manager's Dilemma
Just because the system *allows* you to use `@main` for your internal repo doesn't mean you should.

**The Golden Rule:** * Use **SHAs** for code you don't own (`actions/`).
* Use **Tags** (`@v1`) for code you do own (`cert-labs-hq/`).
* Use **Branches** (`@main`) **NEVER** in a workflow file—only during active local testing.

# 🛡️ GitHub Actions Governance: Enforcing SHAs

## ⚙️ The Policy Mechanism
GitHub allows Organization and Repository admins to restrict how Actions/Workflows are called.
* **Location:** `Settings > Actions > General > Allow specified actions and reusable workflows`.
* **The "Golden" Setting:** "Allow actions and reusable workflows created by GitHub" + "Allow specified actions and reusable workflows."

## 🚦 Result of Policy Activation
Once "Allow specified actions" is configured with a strict list or versioning requirement:
1. **Blocked:** `@main`, `@master`, `@develop`.
2. **Blocked:** Mutable tags like `@v1` (if the policy is set to "Strict").
3. **Allowed:** Full length Commit SHAs (e.g., `40 characters`).

## 🧠 Why this is "Elite" DevOps
* **Repeatability:** You are guaranteed that the code running today is exactly the code that was audited yesterday.
* **Audit Trail:** If a bug occurs, the SHA tells you exactly which commit was responsible. A tag (`@v1`) can be moved, making it harder to find the "smoking gun."

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

> Interestingly, even when "Require SHA pinning" is active, **reusable workflows** can often still be referenced by **tag**. This is because reusable workflows are typically internal and managed by your own organization, reducing the third-party risk.

When calling external code (Workflows or Actions), you have three levels of security:

| Level | Reference | Risk | Convenience |
| :--- | :--- | :--- | :--- |
| **Low** | `@main` | **High.** Code can change any second. | ⭐⭐⭐⭐⭐ |
| **Medium** | `@v1` | **Moderate.** Tags can be moved (Mutable). | ⭐⭐⭐⭐ |
| **High** | `@a1b2c3d` | **Zero.** Commits are Immutable. | ⭐ |

# 🛡️ Immutability & Pinning (Domain 5)

## 🔒 Best Practices
1. **Pin by SHA:** For production/enterprise workflows to prevent "Tag Jumping."
2. **Use Dependabot:** To manage the chore of updating SHAs.
3. **Verified Creators:** Only use actions from "blue-check" creators (Google, AWS, GitHub) in sensitive pipelines.

## 🎓 GH-200 Exam Fact
The exam will ask: "What is the most secure way to reference an action?"
* **Answer:** Use the full commit SHA.

## 🛡️ Best Practices for the Release Manager
1. **Third-Party Actions:** (e.g., `actions/checkout`) -> **Pin to SHA** if you are in a high-security environment (FinTech, Healthcare).
2. **Internal Automation:** -> **Pin to Major Tag (`@v1`)**. This allows the DevOps team to push critical bug fixes (Patch/Minor) without breaking every caller repo.
3. **External Organization Workflows:** If you don't own the other org, treat it like a Third-Party Action and **Pin to SHA**.

# 🛡️ Recursive SHA Pinning in Composite Actions

When an organization-level policy enforces "Full-length commit SHA" pinning, it applies to the entire execution tree.

## 🗝️ The Rule
* **Direct Actions:** Actions called in your workflow `.yml` must use SHAs.
* **Transitive Actions:** Actions called *inside* a Composite Action must **also** use SHAs.

## 🔄 The Maintenance Loop
1. **Automations Repo:** Receives Dependabot PRs to update its internal steps (e.g., `setup-node` v4 -> v6).
2. **Merge & Tag:** You merge the SHA update and create a new tag (e.g., `v1.2.0`) for your composite action.
3. **External Repo:** Receives a Dependabot PR to update the SHA of your `smart-setup` action to the one corresponding to `v1.2.0`.

## ⚠️ Common Pitfall
If you update the `ExternalRepo` but forget to update the `AutomationsRepo`, the workflow will fail with:
`"The actions... are not allowed... because all actions must be pinned to a full-length commit SHA."`

# 🤖 Automating DevOps Updates with Dependabot

To ensure developers are "warned" when you update your Centralized Actions/Workflows, you must configure Dependabot to monitor your internal ecosystem.

## 🛠️ The `dependabot.yml` Configuration
Place this in `.github/dependabot.yml` of every **App Repo**:

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "daily"
    # This tells Dependabot to look for updates to your internal actions
    allow:
      - dependency-name: "<org|user>/*"
```

# 🛡️ Defending the Template: Solving the Sync Problem

When "Version Updates" (Dependabot) aren't enough because the YAML structure itself must change.

## 🥇 Strategy A: Total Abstraction (The "Skinny" Goal)
* **Concept:** Move ALL variables and environment logic into the Reusable Workflow.
* **Result:** The caller file in the App Repo becomes "Static." It never needs to change because it just says "Run the standard process."

## 🥈 Strategy B: The Compliance Diff (The "Warning")
* **Concept:** A CI step that compares the local YAML to a remote "Golden Master."
* **Result:** Developers get an immediate "Warning" or "Failure" if their local file is outdated or tampered with.

## 🥉 Strategy C: Required Workflows (The "Kill Switch")
* **Concept:** Use GitHub Enterprise features to inject workflows.
* **Result:** The developer doesn't own the file, so they can't break it, and you never have to sync it.

# 🛡️ Pattern: Template Integrity Guard

For accounts without "Required Workflows" (Enterprise), use a **Comparison Job** to detect configuration drift.

## ⚙️ How it works
1. **Source of Truth:** A "Golden Master" YAML is stored in the Central Automations Repo.
2. **The Comparison:** The Compliance workflow runs a `diff` between the local file and the Master.
3. **The Warning:** If changes are found, the `diff` output is printed directly to the **Job Summary** in Markdown.

## ⚖️ Why use this?
* **Visibility:** You can instantly see *exactly* what a developer changed in their workflow.
* **Soft Enforcement:** You can let the build pass but "nag" the developer, or set `exit 1` to block the PR until they revert to the standard.
* **Automation:** Use this to catch missing `concurrency` groups or unauthorized `permissions` changes.

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