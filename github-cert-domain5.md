[Github cert](github-cert.md)

# Environments

> Environment Protection Rules (like Required Reviewers and Wait Timers) are a GitHub Enterprise and GitHub Pro/Team feature.

```text
The Exam Scenario: The exam might ask, "You have a developer using a personal fork to test a deployment. Why are the Environment Protection Rules not triggering?"

The Answer: Because the rules are defined in the Organization's environment, and the personal fork does not inherit the Org's environment settings.
```

### ⏳ Environment Wait Timers
* **Function:** A "forced cooldown" (in minutes) before a job starts.
* **Use Case:** Allowing for "Bake Time" in Staging or waiting for cloud DNS/DB migrations to stabilize.
* **Interaction:** If a **Reviewer** and **Wait Timer** are both present:
  1. The Timer starts immediately.
  2. The Reviewer can approve at any time.
  3. The Job **only starts** when the Timer reaches 0 **AND** the Reviewer has approved.

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
```

> * Token Scoping: Fine-grained PATs must have the Organization as the Resource Owner to modify Org repos; otherwise, you get the 403 you encountered.
> * The "Metadata" Base: Every token needs at least metadata: read to even see a repository's exists via the API.
> * To create repos you need the Administration and Content permission (read and write)

# 🛡️ GitHub Enterprise Governance & Scaling

## 1. Branch Protection vs. Repository Rulesets
| Feature | **Branch Protection Rules** | **Repository Rulesets** |
| :--- | :--- | :--- |
| **Scope** | Single Repository only. | **Organization-wide** or per-repo. |
| **Scaling** | Manual per repo. | **Auto-target** via topics/regex. |
| **Enforcement** | Admin Bypass is a simple toggle. | **Granular Bypass** (Roles/Apps/Teams). |
| **Evaluation** | Always "Live." | Supports **"Evaluate Mode"** (Dry run). |

> **💡 GH-200 Exam Note:** Rulesets are the modern standard. They allow you to enforce a PR policy across **all** repositories in an Org with a single Terraform resource.

---

## 2. The Hierarchy of Secrets & Variables
GitHub uses an **Inheritance Model**. A workflow job can "see" data from multiple layers:

1.  **Organization Level:** Shared across the whole company (e.g., `SONAR_TOKEN`).
2.  **Repository Level:** Specific to the application code (e.g., `APP_NAME`).
3.  **Environment Level:** Specific to the deployment target (e.g., `PROD_DB_URL`).

**Priority Rule:** If a secret name exists at multiple levels, the **most specific** one wins (Environment > Repo > Org).

---

## 3. Environment Governance (The "Deployment Gate")
Environments are **Repository-Scoped** boundaries. They do not exist at the Org level because they hold target-specific secrets.

* **Manual Approvals:** Pause a workflow until a designated user/team clicks "Approve."
* **Wait Timers:** Force a delay (e.g., 10m) before deployment to allow for manual smoke tests.
* **Branch Policies:** Hard-restrict which branches can deploy to which environments (e.g., Only `main` can deploy to `production`).
* **Prevent Self-Review:** Ensures the person who merged the PR cannot be the one to approve the deployment.

---

## 4. Terraform Scaling (The "Central Infra" Pattern)

### Example: Scaling via Modules
```hcl
# main.tf
module "standard_repo" {
  for_each    = toset(["api-service", "frontend-app", "auth-worker"])
  source      = "./modules/gh-repo"
  repo_name   = each.key
  description = "Managed by Central DevOps Team"
}
```

### Example: Organization Ruleset
```hcl
resource "github_organization_ruleset" "global_main_protection" {
  name        = "enforce-pr-and-checks"
  target      = "all"
  enforcement = "active"

  rules {
    pull_request {
      required_approving_review_count = 1
    }
    required_status_checks {
      required_check {
        context = "test_and_scan"
      }
    }
  }
}
```

---

## 5. Summary: The "Chain of Trust"
To achieve **SOC2/ISO Compliance**, follow this sequence:
1.  **Code Gate:** Rulesets/Branch Protection (Ensures the code is reviewed).
2.  **Health Gate:** Status Checks (Ensures the code is passing tests/security scans).
3.  **Deployment Gate:** Environments (Ensures a human authorized the infrastructure change).

# 🛡️ Mitigating Script Injection in GitHub Actions

In **Domain 5 (Secure and Optimize Automation)**, one of the most critical risks is **Script Injection**. This occurs when untrusted user input (like an issue title, PR comment, or branch name) is executed as a command.

### ❌ The Vulnerable Way (Direct Expression Expansion)
Using `${{ ... }}` directly inside a `run:` block allows a malicious actor to "break out" of your command and execute their own.

```yaml
on:
  issues:
    types: [opened]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - name: Log Issue Title
        # ⚠️ VULNERABLE: Direct injection point
        run: echo "Processing issue: ${{ github.event.issue.title }}"

#When the runner executes the command, the shell sees:
#echo "Hello, the issue title is: Awesome Feature" ; rm -rf / ; echo "Hacked"
# - It echoes the title
# - It executes rm -rf /, attempting to wipe your runner's file system
# - It echoes "hacked"

on:
  issues:
    types: [opened]

...
# 🛡️ SECURE: Map to an env variable first
      - name: Greet user
        env:
          ISSUE_TITLE: ${{ github.event.issue.title }}
        # Use the shell variable ($ISSUE_TITLE), not the expression
        run: echo "Hello, the issue title is: $ISSUE_TITLE"

```

* Rule 1: Never use ${{ ... }} inside run: for data that a user can influence (Issues, PRs, Labels, Branch names).
* Rule 2: Always map untrusted data to an env: block at the step or job level.
* Rule 3 (Defense in Depth): Combine this with Least-Privilege Permissions. Even if a script is injected, ensure the GITHUB_TOKEN only has contents: read so it cannot modify your repository.

```yml
permissions:
  contents: read
  issues: read
```

# OIDC: Eliminating Long-Lived Cloud Secrets 

## 1 The Workflow Configuration
To use OIDC, your workflow needs a specific permission called id-token. This allows the runner to fetch a JWT (JSON Web Token) from GitHub to present to AWS.

```yml
jobs:
  deploy:
    runs-on: ubuntu-latest
    # 🛡️ MANDATORY for OIDC
    permissions:
      id-token: write   # This is required to request the JWT
      contents: read    # This is required for actions/checkout
      
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          # This is the ARN of the IAM Role you create in AWS
          role-to-assume: arn:aws:iam::123456789012:role/my-github-actions-role
          aws-region: us-east-1

      - name: Deploy to ECS
        run: |
          aws ecs update-service --cluster my-cluster --service my-app --force-new-deployment
```

## 2 The AWS Side (The Trust Relationship)
For this to work, you must create an Identity Provider in AWS IAM and a Role with a "Trust Policy." This policy tells AWS: "I trust tokens from GitHub, but only if they come from MY organization and MY repository."

Example Trust Policy (The Logic):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { "Federated": "arn:aws:iam::123456789012:oidc-provider/token.actions.githubusercontent.com" },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:C11R11/test-repo:*"
        }
      }
    }
  ]
}
```
## 📊 Why OIDC is a "Domain 5" Win

| Feature | Old Way (Static Secrets) | New Way (OIDC) |
| :--- | :--- | :--- |
| **Secret Management** | Requires manual rotation/storage in GitHub Secrets. | **Zero maintenance.** No permanent keys to manage. |
| **Security Risk** | Permanent keys can be leaked or stolen from the repo. | **Tokens expire** automatically when the job finishes. |
| **Governance** | Hard to limit a static key to a specific repository. | **Scope access** to specific organizations, repos, or branches. |
| **Compliance** | Fails many modern security audits (PCI/SOC2). | **Industry Gold Standard** for cloud federation. |

## Important

* The Permission: If you forget id-token: write, the aws-actions/configure-aws-credentials action will fail with a "Could not fetch ID token" error.
* The Action Version: You must use at least v2 or higher of the AWS action to support OIDC.
* Scoped Trust: You can restrict the trust policy so that only the main branch can assume the "Production" role, preventing a developer from deploying from a feature branch.


# Artifact Attestations & Provenance

In Domain 5, this is referred to as Software Bill of Materials (SBOM) and SLSA (Supply-chain Levels for Software Artifacts).

## The Core Concept

An Attestation is a digitally signed document that says: "I, GitHub Actions, built this specific file (SHA-256) using this specific workflow on this specific branch." If someone tries to swap your compiled .exe or Docker image with a malicious version, the Verification step will fail because the signature won't match.

## The Implementation (Generating Proof)

GitHub provides a built-in action to handle the heavy lifting. It uses Sigstore (a passwordless signing service) under the hood.

```yml
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write      # Required for Sigstore signing
      attestations: write  # Required to write the attestation to GitHub
      contents: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Build Artifact
        run: echo "Final Build" > my-app.zip

      - name: Generate Attestation
        uses: actions/actions/attest-build-provenance@v1
        with:
          subject-path: 'my-app.zip'
```

## The Verification (Checking Proof)
On your deployment server (or even on your local Mac Mini), you can verify that the file is legitimate using the GitHub CLI (gh).

```sh
# Verify the file before deploying it
gh attestation verify my-app.zip --repo C11R11/test-repo

#If the file was tampered with, you will get a scary red error. If it’s safe, you’ll see "Verification succeeded!"
```

## 📊 Why Artifact Attestations are a "Domain 5" Win

| Feature | Without Attestations | With Attestations (SLSA) |
| :--- | :--- | :--- |
| **Tamper Detection** | None. A malicious actor could swap a file in S3/ECR after the build. | **Instant Detection.** If even one byte is changed, the digital signature will not match. |
| **Origin Trust** | "Implicit" trust. We hope the file in our bucket is the one we built. | **"Zero Trust"** model. The file carries its own proof of origin (who, when, and where). |
| **Audit/Compliance** | Very difficult to prove the exact source code for a specific binary. | **Full Traceability.** Every artifact is linked to a specific GitHub PR, Commit, and Workflow run. |
| **Standard** | No standardized metadata. | **SLSA Level 3 compliant.** Meets the highest industry standards for supply chain security. |

## Important 

* The attestation is detached metadata stored on GitHub's backend, not inside your file.
  * It calculates the unique SHA-256 fingerprint of your file
  * It creates a separate JSON document (the Attestation) that contains that SHA-256 hash, the workflow name, the run ID, and the commit SHA.
  * It signs that document using Sigstore and stores it in a secure "Attestation Store" associated with your GitHub repositor
* When you run gh attestation verify, the CLI sends the hash of your local file to GitHub and asks: "Do you have a signed certificate that matches this fingerprint?"
* The Tool: The exam might mention SLSA (pronounced "Salsa"). Just remember: Attestations = SLSA Level 3 compliance.
* The Permissions: You must have id-token: write because the signing process uses the same OIDC technology we just discussed for AWS.
* The Subject: You can attest files, Docker images, and even entire directories.

# 📈 Efficiency: Caching & Artifact Retention Management

In **Domain 5 (Secure and Optimize Automation)**, the focus is on reducing build times and controlling storage costs through intelligent cleanup and API-driven management.

## 1. Artifact Retention Policies
By default, GitHub keeps artifacts for 90 days. For a DevOps engineer, this is usually unnecessary and expensive. You can override this at the **Workflow level** or the **Job level**.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Upload Production Build
        uses: actions/upload-artifact@v4
        with:
          name: release-binary
          path: bin/
          # 🛡️ DOMAIN 5: Minimal retention for security and cost
          retention-days: 5
```
## 2 Programmatic Cleanup (GitHub REST API)
Sometimes you need to delete artifacts immediately after a successful deployment to free up space or remove sensitive binaries.

Example: Delete an artifact via cURL (REST API)

```sh
# 1. List artifacts to find the ID
curl -L -H "Authorization: Bearer $TOKEN" \
  [https://api.github.com/repos/OWNER/REPO/actions/artifacts](https://api.github.com/repos/OWNER/REPO/actions/artifacts)

# 2. Delete the specific artifact
curl -L -X DELETE -H "Authorization: Bearer $TOKEN" \
  [https://api.github.com/repos/OWNER/REPO/actions/artifacts/ARTIFACT_ID](https://api.github.com/repos/OWNER/REPO/actions/artifacts/ARTIFACT_ID)
```

## 3 Advanced Caching (Dependency Management)
While you know the basics of actions/cache, Domain 5 requires understanding Cache Hits/Misses and Key Optimization.

Cache Hit: The key matches exactly; the folder is restored.

Cache Miss: No match; the workflow must download everything and then save a new cache.

Optimization Strategy:
Use a "Fallback" key (restore-keys) so that even if your package-lock.json changes slightly, you still get most of your dependencies back.

```yml
- name: Cache Dependencies
  uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

## 📊 Strategy Comparison: Cache vs. Artifacts

| Feature | **Caching** (`actions/cache`) | **Artifacts** (`upload-artifact`) |
| :--- | :--- | :--- |
| **Purpose** | Speed up the *current* or *future* workflow runs. | Save files for *human* download or *external* use. |
| **Common Contents** | Dependencies (`node_modules`, `.m2`, `NuGet`). | Binaries, Test Reports, Build Metadata, Logs. |
| **Lifecycle** | Deleted automatically if not accessed in 7 days. | Retained based on `retention-days` (Default 90). |
| **Accessibility** | Internal to GitHub Actions only (Scoped by branch). | Downloadable via the UI, API, or GitHub CLI. |
| **Storage Limit** | **10GB per repository** (FIFO cleanup). | Shared across the Enterprise/Org storage quota. |

## Important

* Cache Limits: Each repository has a 10GB limit for total cache size. If you exceed it, GitHub will delete the oldest caches to make room.
* Scope: Caches are isolated by branch. A feature branch can access the main branch cache, but the main branch cannot access a feature branch cache (for security).
* The API: You can manage both Caches and Artifacts via the GitHub CLI: gh cache list and gh cache delete --all.

# ⚡ Scaling and Optimization: High-Performance Workflows

In **Domain 5 (Secure and Optimize Automation)**, the focus is on reducing the "Time to Feedback." For a DevOps engineer, this means running tasks in parallel without hitting resource limits or wasting runner minutes.

## 1. Matrix Strategy (Parallelization)
Instead of creating three separate jobs for your different environments, use a `matrix`. This spawns multiple jobs simultaneously.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      # 🚀 DOMAIN 5: If one version fails, keep running the others to see the full impact
      fail-fast: false 
      matrix:
        node-version: [18, 20, 22]
        os: [ubuntu-latest, windows-latest]
    
    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js ${{ matrix.node-version }} on ${{ matrix.os }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
```

## 2 Concurrency Groups (Resource Protection)
In your Terraform or Deployment pipelines, you cannot have two jobs running at the same time (it would cause a "State Lock" error in AWS). Concurrency ensures only one run happens at a time.

```yml
# 🛡️ DOMAIN 5: Cancel any previous runs on the same branch if a new one starts
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: self-hosted # Your Mac Mini
    steps:
      - run: terraform apply -auto-approve
```

## 3 Shallow Clone (Checkout Optimization)
By default, actions/checkout downloads the entire history of your repository. For a simple build or test, you only need the latest code.

```yml
steps:
  - name: Fast Checkout
    uses: actions/checkout@v4
    with:
      # ⚡ DOMAIN 5: Only download the last commit (immense speed gain for large repos)
      fetch-depth: 1
```

## 📊 Optimization Strategy Matrix

| Feature | **What it solves** | **DevOps Impact** |
| :--- | :--- | :--- |
| **Matrix** | Slow, sequential testing across multiple versions. | **High Speed:** Reduces "Time to Ship" by 3x-5x through parallel execution. |
| **Concurrency** | Multiple deployments colliding or overlapping. | **Stability:** Prevents Terraform state locks and "Double-Deploy" race conditions. |
| **Shallow Clone** | Long checkout times, especially on large legacy repositories. | **Efficiency:** Massive reduction in network overhead and runner disk usage. |
| **Fail-Fast: false** | Stopping the whole run on the first minor error. | **Visibility:** Allows you to see the full impact across all versions in a single run. |

## Important 

* Matrix Limits: You can run up to 256 jobs in a single matrix, but GitHub will only run a certain number in parallel depending on your plan (Free vs. Pro).
* Include/Exclude: You can use include: to add a specific combination to a matrix (like "Only run Node 22 on macOS") without adding it to the whole grid.
* Fetch-Depth: If your workflow needs to calculate a version number based on Git Tags, fetch-depth: 1 will break it. You would need fetch-depth: 0 for that specific case.

# 🔑 Security: GITHUB_TOKEN Lifecycle & Granular Permissions

In **Domain 5 (Secure and Optimize Automation)**, the objective is **Least Privilege**. We want every job to have exactly the power it needs—and not a single drop more.

### 1. The GITHUB_TOKEN (The "Smart" Choice)
Every time a workflow run starts, GitHub automatically creates a unique, temporary installation token called `GITHUB_TOKEN`.

* **Lifecycle (Ephemeral):** The token is created when the job starts and **expires automatically** when the job finishes (or after a maximum of 24 hours).
* **Security:** If a runner is compromised, the attacker only has a short window to use the token before it becomes useless.
* **Permissions (Scoped):** By default, in a new organization, this token is **Read-Only**. You must explicitly grant it "Write" access in your YAML.

### 2. Personal Access Tokens (PATs) (The "Legacy" Risk)
A PAT is tied to a specific **human user**. 
* **Risk:** If a PAT is leaked, it doesn't expire. An attacker can use it to access *every* repository that user has access to.
* **Context:** Only use a PAT if your Action needs to reach **outside** its own repository (e.g., to trigger a workflow in a different repo).

---

### 3. Hardening: Granular Permissions
To follow the "Least Privilege" principle, you should define a `permissions:` block at the top of your workflow or within a specific job.

```yaml
# 🛡️ DOMAIN 5: Define the baseline at the top
permissions:
  contents: read      # Can only see the code, not push to it
  pull-requests: write # Allows adding comments or labels to PRs
  id-token: write      # Required for OIDC/AWS connections
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test
```

## 📊 Comparison: GITHUB_TOKEN vs. Personal Access Token (PAT)

| Feature | **GITHUB_TOKEN** | **Personal Access Token (PAT)** |
| :--- | :--- | :--- |
| **Expiration** | **Automatic.** Created at job start; expires when the job finishes. | **Manual.** Set by the user (e.g., 30, 60, 90 days, or "No Expiration"). |
| **Scope** | **Current Repository only.** Cannot access other private repos in your Org. | **Account-wide.** Accesses any repo the user can see (Higher Risk). |
| **Management** | **None.** Injected automatically by GitHub; no secrets to store. | **Manual.** Must be stored as a GitHub Secret and rotated manually. |
| **Triggering** | **Does NOT trigger** subsequent workflows (Prevents infinite loops). | **CAN trigger** other workflows upon code push or PR creation. |
| **Best Practice** | Used for 90% of CI/CD, testing, and cloud deployments. | Used only for cross-repo tasks or Org-level automation. |

## GITHUB_TOKEN Isolation:

* Every Workflow Run gets a unique token.
* Every Job within that run uses that same token (unless specified otherwise).
* Never pass tokens between workflows. Let each workflow use its own ${{ github.token }} to ensure the permissions and expiry match the actual work being done.

## Important

* The "Infinite Loop" Protection: If an Action uses a GITHUB_TOKEN to push code back to the repo, GitHub will not trigger a new workflow run based on that push. This prevents accidental infinite loops. If you want to trigger another workflow, you must use a PAT.
* Organization Default: In the GitHub UI (Settings -> Actions -> General), you can set the default permission for the token to either "Read/Write" or "Read-Only." Domain 5 recommends setting the default to Read-Only and enabling write access only in the YAML.
* Environment Secrets: Permissions apply to the token, but they do not bypass Environment Protection rules (like the manual approval you set up earlier).

> Question: "You have a modular workflow system. Should you pass the GITHUB_TOKEN from a caller to a callee?"

* The Answer: No. You should rely on the Callee's own GITHUB_TOKEN.

  * Why? Because GitHub Actions is designed so that every job gets its own fresh, isolated identity. Passing tokens breaks the "Isolation" principle and creates "Race Conditions" where a token might expire while the second workflow is still using it.

## 📂 Cross-Repository Governance: Reusable Workflows

In **Domain 5**, managing how workflows interact across repository boundaries is a key security task. 

### 🔑 The Golden Rule of Tokens
When **Repo A** calls a Reusable Workflow in **Repo B**, the `GITHUB_TOKEN` belongs to **Repo A** (the Caller).

* **Access to Repo A:** Automatic (based on permissions in Repo A's YAML).
* **Access to Repo B:** **Denied.** The token is scoped only to the repository where the event was triggered.

### 🛠️ Example: Accessing the Caller's Code
If you want a shared workflow (Repo B) to build the code in your project (Repo A):

**The Calls (From repo A to repo b):**
```yaml
jobs:
  call-shared-logic:
    permissions:
      contents: read # 🛡️ Grant the token permission to read Repo A
    uses: C11R11/repo-b/.github/workflows/reusable.yml@main

# Then in the reusable.yml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo A
        uses: actions/checkout@v4 # 💡 Uses Repo A's token automatically
```

### What if you need to access Repo B too?
If your script in Repo B needs to download a private tool or secondary code from itself (Repo B), the GITHUB_TOKEN will fail. You must pass a Secret (PAT or GitHub App Token) to the reusable workflow.

The "Secure" Implementation:

```yml
# In Repo A (Caller)
jobs:
  call-shared:
    uses: C11R11/repo-b/.github/workflows/reusable.yml@main
    secrets:
      # 🔐 Pass a PAT that has access to Repo B
      REPO_B_TOKEN: ${{ secrets.MY_PERSONAL_ACCESS_TOKEN }}
```