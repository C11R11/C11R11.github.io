# 🛣️ Branching Strategies Cheatsheet (Git Workflow Patterns)

### 1. GitHub Flow (Simple & Fast)
* **Core Philosophy:** Anything in the `main` branch is always deployable.
* **The Process:**
    1. Create a **Feature Branch** from `main`.
    2. Commit changes and push.
    3. Open a **Pull Request (PR)** for discussion/CI.
    4. Merge into `main` and deploy immediately.
* **Best For:** Web apps, SaaS, and teams practicing **Continuous Deployment (CD)**.
* **Pros:** Extremely simple; minimal merge conflicts.
* **Cons:** No "Staging" area; hard to manage scheduled "Big Releases."

### 2. GitFlow (Structured & Scheduled)
* **Core Philosophy:** Strict separation between "Work in Progress" and "Production."
* **The Five Branches:**
    1. `main`: Production-ready code only.
    2. `develop`: The integration branch for features.
    3. `feature/`: Short-lived branches for new code (merges into `develop`).
    4. `release/`: Preparation for a new production release (merges into `main` and `develop`).
    5. `hotfix/`: Emergency fixes for production (merges into `main` and `develop`).
* **Best For:** Legacy software, mobile apps, or regulated industries with strict release cycles.
* **Pros:** Highly organized; clear versioning history.
* **Cons:** Complex; "Merge Hell" is common; slow for fast-moving teams.

### 3. Trunk-Based Development (High Velocity)
* **Core Philosophy:** All developers work on a single branch (`main` or "the trunk").
* **The Process:**
    * Developers push small, frequent updates directly to `main` (or via very short-lived PRs < 24 hours).
    * Uses **Feature Flags** to hide unfinished code from users.
* **Best For:** High-performing DevOps teams (Netflix, Google, Amazon).
* **Pros:** Fastest feedback loop; eliminates long-lived branch drift.
* **Cons:** Requires high senior expertise and 100% automated test coverage.

---

### 📊 Strategy Comparison Matrix

| Feature | GitHub Flow | GitFlow | Trunk-Based |
| :--- | :--- | :--- | :--- |
| **Primary Branch** | `main` | `develop` | `main` |
| **Release Frequency** | Multiple times/day | Weeks or Months | Hourly / On-Demand |
| **Complexity** | Low | **High** | Medium |
| **CI/CD Fit** | Excellent | Hard | **Perfect** |
| **Code Review** | On Every PR | On Release/Feature | Continuous / Small PRs |

---

### 🎓 GH-200 Exam "Key Concepts"
* **Branch Protection:** Regardless of strategy, the `main` branch should **always** be protected (require PRs, require status checks).
* **Environment Mapping:** * In **GitFlow**, the `develop` branch usually triggers a "Staging" deployment.
    * In **GitHub Flow**, the `main` branch triggers a "Production" deployment.
* **Reference Context:** Use `${{ github.ref }}` in your YAML to detect which branch triggered the run and apply different logic (e.g., `if: github.ref == 'refs/heads/main'`).

# 📑 GitHub Merge Strategies Cheatsheet

### 1. Create a Merge Commit (`--no-ff`)
* **Mechanism:** Combines all commits from the feature branch and creates a unique **Merge Commit** to tie the histories together.
* **The Visual:** A "diamond" or "loop" shape in the Git graph.
* **Best For:** **GitFlow** (e.g., merging `develop` into `main`) where preserving the full history of every single developer's commit is required for auditing.
* **DevOps Impact:** Preserves the context of when a feature was integrated, but can make the history "noisy."

### 2. Squash and Merge
* **Mechanism:** Combines (squashes) **all** commits from the PR into **one single commit** on the target branch.
* **The Visual:** A perfectly straight, linear line on the `main` branch. The "messy" intermediate commits (e.g., "fixed typo," "trying again") are deleted.
* **Best For:** **GitHub Flow** and **Feature Branches**. It keeps the production history clean and professional.
* **DevOps Impact:** **High.** Excellent for rollbacks because you only have to revert **one** commit to remove an entire feature.

### 3. Rebase and Merge
* **Mechanism:** Moves each individual commit from the feature branch onto the tip of the target branch one by one.
* **The Visual:** A straight, linear line (like Squash), but **all** individual commits are preserved as separate entries.
* **Best For:** Teams that want a linear history but need to see the granular detail of every small change.
* **DevOps Impact:** **Caution.** Rewrites commit SHAs, which can occasionally cause confusion in automated tracking systems if not managed carefully.

---

### 📊 Strategy Comparison Table

| Strategy | History Style | Intermediate Commits? | Best Use Case | Rollback Ease |
| :--- | :--- | :--- | :--- | :--- |
| **Merge Commit** | Recursive (Loops) | **Preserved** | GitFlow / Releases | Medium |
| **Squash** | Linear (Straight) | **Hidden** (Cleaned) | Feature Branches | **Easy** (1 Commit) |
| **Rebase** | Linear (Straight) | **Preserved** | Expert/Small Teams | Hard (Many) |

---

### 🛡️ DevOps Governance: Enforcing via Repository Settings
As a DevOps Engineer, you can enforce these rules in GitHub to prevent "messy" histories:

1. **Navigate to:** `Settings` -> `General` -> `Pull Requests`.
2. **Options:**
   - [ ] Allow merge commits
   - [x] **Allow squash merging** (Recommended)
   - [ ] Allow rebase merging
3. **Outcome:** By unchecking the others, the "Merge" button on PRs will only allow the **Squash** method, forcing a clean history for your production environment.