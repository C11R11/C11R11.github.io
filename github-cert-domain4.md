# Domain 4: Enterprise Management

## 🛡️ SHA Policy Inheritance & Recursive Enforcement

In a professional environment, security policies are "Execution-Time" checks, not "Source-Time" checks.

| Scenario | Org/Repo Policy | Action Code | Result |
| :--- | :--- | :--- | :--- |
| **Direct Call** | SHA Required | `uses: action@v4` | ❌ Blocked |
| **Composite Call** | SHA Required | `uses: my-org/my-action@SHA` | 🔍 Investigating... |
| **Nested Step** | SHA Required | `  uses: actions/cache@v4` | ❌ Blocked |

### 💡 The "Trust" Logic
The `ExternalRepo` says: "I don't trust any code that isn't pinned." Since a Composite Action is just a collection of scripts and actions being 'pasted' into the runner, the `ExternalRepo` applies its rules to every single line of that 'pasted' code.

### ✅ The Solution
To make a Centralized Action compatible with "High Security" subscriber repos, the Central Repo must **voluntarily** use SHAs for all internal steps, even if its own policy doesn't require it.

# 🧹 Cache Management & Governance

While caching speeds up builds, it requires monitoring in an enterprise environment to stay within limits.

## 1. Storage Limits
* **Quota:** 10GB per repository.
* **Eviction Policy:** Once the 10GB limit is reached, GitHub automatically deletes the oldest caches to make room for new ones (Least Recently Used / LRU).
* **Retention:** Caches are automatically deleted if not accessed for **7 days**.

## 2. Accessing the Cache UI
* **Path:** `Actions` -> `Management` (left sidebar) -> `Caches`.
* **Purpose:** Use this screen to manually delete corrupted caches or free up space if you notice "Cache Eviction" happening too frequently.

## 3. The GitHub CLI (`gh`) Approach
As a DevOps engineer, you might prefer the terminal for bulk management:

# 🏗️ Architectural Pivot: File-Sync vs. Action-as-a-Service

As a DevOps Engineer, moving from managing individual `.github/workflows` files to centralized Actions is a major "Maturity Level" upgrade.

## ⚖️ Comparison Matrix

| Feature | File-Sync Pattern (Previous) | Action-as-a-Service (Current Lab) | The "Senior DevOps" Win |
| :--- | :--- | :--- | :--- |
| **Logic Delivery** | Terraform "Pushes" `.yml` files to 100+ repos. | Repos "Pull" logic via `uses: automations/action@SHA`. | **Maintenance:** Fix a bug in 1 repo vs. 100 repos. |
| **Tamper Resistance** | Developers can edit/delete the local `.yml` file. | Logic is "Black-Boxed" inside the Composite Action. | **Compliance:** Ensures the Security Scan *actually* runs as intended. |
| **Secret Surface Area** | Secrets are copied/duplicated to every repository. | Centralized OIDC or Org-level Secret access. | **Security:** If a key rotates, you update it in 1 place, not 100. |
| **Version Control** | Every repo must be on the same "Sync" cycle. | Repos can pin to specific SHAs/Tags of the Action. | **Stability:** Teams can upgrade to the "New Build Logic" at their own pace. |
| **Drift Management** | Requires frequent TF runs to "re-sync" changed files. | Zero Drift. The source of truth is the `AutomationsRepo`. | **Reliability:** No "Ghost changes" in hidden workflow files. |

---

## 🛠️ The "Polymorphic" Strategy (Action-as-a-Service)

Instead of having a `node-ci.yml` and a `dotnet-ci.yml`, you build a **Smart Action** that detects the environment.

### The Evolution of the "Smart-Setup"
1. **Old Way:** Terraform copies `node-setup.yml` if the repo is tagged "Node".
2. **New Way:** Your `smart-setup` composite action uses a shell script to check for files:
   - `if [ -f package.json ]; then run npm ci; fi`
   - `if [ -f *.csproj ]; then run dotnet restore; fi`

## 🎯 GH-200 Domain Link: 3 & 4
* **Domain 3 (Actions):** Focuses on creating these "Black-Box" tools that abstract complexity.
* **Domain 4 (Enterprise):** Focuses on "Enforced Policies" where you require these Actions to be used across the whole Organization.

# 🧨 The "Cut Cord" Problem: Templates vs. References

When managing 100+ repos, you must choose between **Flexibility** (Templates) and **Control** (References).

## 1. Starter Workflows (Flexibility)
* **Mechanism:** Copy-on-creation.
* **The "Warning" Problem:** None. The developer owns the file now.
* **Maintenance:** Manual. You must use Terraform or a script to "Force-Push" updates to 100 repos.
* **Use Case:** "Example" workflows or non-critical tasks.

## 2. Reusable Workflows (Control)
* **Mechanism:** Referencing a remote file via `uses:`.
* **The "Warning" Problem:** Automated via **Dependabot**.
* **Maintenance:** Centralized. Update the code in the `AutomationsRepo` and it applies to everyone.
* **Use Case:** Security Gates, Production Deploys, and Compliance.

## ⚖️ The "Hybrid" Best Practice
Keep the YAML in the developer's repo as a "Skinny Caller."
* **The Developer's File:** Only contains `on: push` and 5 lines of code calling your Reusable Workflow.
* **The Logic:** 100% inside your `AutomationsRepo`.

# 📉 Handling Template Drift

When developers own a copy of a workflow file, the "Source of Truth" becomes fragmented. 

## 🛡️ Level 1: The "Skinny Caller" (Prevention)
* **Goal:** Reduce the developer's YAML to < 10 lines.
* **Mechanism:** Use `uses: C11R11/AutomationsRepo/.github/workflows/node.yml@v1`.
* **Benefit:** Since 99% of the code lives in your repo, you can change the "Template" (add steps, change logic) and the developer gets the update instantly without touching their file.

## 🔍 Level 2: The "Checksum" Check (Detection)
* **Goal:** Warn developers if they modified the "Standard" YAML.
* **Mechanism:** A CI step that runs a `diff` between the local `.github/workflows/main.yml` and a "Master" file in the `AutomationsRepo`.
* **Action:** Fail the build or post a "Warning" if the files don't match.

## ⚡ Level 3: Required Workflows (Enforcement - GHE Only)
* **Goal:** 100% Control.
* **Mechanism:** The Organization "Injects" the workflow at runtime. 
* **Benefit:** The developer has **zero** YAML files in their repo. They cannot see, edit, or delete the logic. You have 100% certainty that the latest "Template" is running.

# 🚀 Scaling GitHub Actions: Two Paths to Governance

| Feature | The "Bootstrap" (Free/Team) | The "Injection" (Enterprise) |
| :--- | :--- | :--- |
| **Effort** | Developer must add 1 "Skinny" file. | Zero effort from developers. |
| **Control** | High. You manage the "Orchestrator." | Absolute. You enforce via Org Policy. |
| **Update Path** | Update the Orchestrator in the Central Repo. | Update the Policy in the Org Settings. |
| **Risk** | Developer can delete the "Bootstrap" file. | Developer cannot see or stop the workflow. |

## 💡 The "DevOps Rule of One"
If you have to manage more than **one** file in a remote repository, your abstraction is leaking. Move the extra files into nested `workflow_call` logic in your central repository.