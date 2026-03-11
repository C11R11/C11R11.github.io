# 🔄 The Action Release Lifecycle

When you update a Centralized Action, you must follow these steps to ensure "Subscribers" (External Repos) can see the changes.

## 1. Commit & SHA (The Producer)
* Push your internal SHA fixes to `main`.
* Note the new commit SHA of the `automations-repo`.

## 2. Re-Tag (The Signal)
* Create a new Release/Tag (e.g., `v1.0.1`). 
* This signals to the world that a new "Human Friendly" version is ready.

## 3. Update (The Consumer)
* **Manual:** Update the External Repo workflow `uses:` line to the new SHA of the `automations-repo`.
* **Automated:** Wait for Dependabot to detect `v1.0.1` and open a PR with the new SHA.

## ⚠️ The "Ghost Tag" Trap
If you change the code but don't move the Tag, the External Repo will continue to download the "cached" version of the action from the original tag's commit.

# 📚 The Modular Workflow Library Pattern

To avoid "Spaghetti YAML" when managing multiple languages (Node, .NET, Python), use a **Modular Library** approach instead of a "Universal" workflow.

## ⚖️ Strategy: Modular vs. Monolithic

| Feature | Monolithic (Spaghetti) | Modular (Library) |
| :--- | :--- | :--- |
| **Logic** | One file with many `if` checks. | Dedicated files per tech stack. |
| **Maintenance** | High risk; changing Node logic might break .NET. | Low risk; changes are isolated to the specific stack. |
| **Developer Experience** | Confusing inputs (which ones do I need?). | Clear, language-specific inputs. |
| **Exam Context** | Hard to scale. | **Best Practice** for Enterprise Org management. |

## 🛠️ Implementation Tip: "Nested" Reusable Workflows
You can actually have a `node-pipeline.yml` call a `compliance-gate.yml`. 
* **Layer 1:** The App Repo calls the `node-pipeline`.
* **Layer 2:** The `node-pipeline` calls a shared `security-scan` workflow.
* **Benefit:** You only write the "Security Scan" logic **ONCE**, but it's used by Node, .NET, and Python pipelines.

> * In a Composite Action, the shell: bash line is mandatory for every run step. In a standard Workflow job, it is optional (it defaults to bash on Linux), but the GH-200 exam loves to trick you by omitting it in a Composite Action.

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