# 🤖 Automating SHA Updates with Dependabot

Don't manage SHAs manually. Use Dependabot to keep your external workflows secure and up-to-date.

## 1. Create the Config File
Create a file at `.github/dependabot.yml` in your **external repository**.

```yaml
version: 2
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "daily" # Checks for new versions every day
    groups:
      actions:
        patterns:
          - "*" # Groups all action updates into one PR

# 🕵️ Monitoring Dependabot Status

If Dependabot isn't opening PRs, check these three locations:

## 1. The Dependency Graph (The Source of Truth)
* **Path:** `Insights > Dependency graph > Dependabot`.
* **Action:** Click "Check for updates" to force a manual scan.
* **Logs:** View the "Recent update jobs" to see if it encountered a 401/404 error when trying to read your automation repo.

## 2. Repository Settings (The Permissions)
* **Path:** `Settings > Code security and analysis`.
* **Requirement:** Ensure "Dependabot version updates" is **Enabled**.
* **Requirement:** "Dependabot security updates" should also be on for standard actions (like `actions/checkout`).

## 3. The PR Tab
If a new version exists in your `automations-repo` (e.g., a new tag or a more recent commit than your current SHA), Dependabot will automatically create a Pull Request titled:
`Bump cert-labs-hq/automations-repo from <OLD_SHA> to <NEW_SHA>`

# 🔄 Synchronization Cadence

Our automation infrastructure follows a "Pull-Based" update model.

| Event | Trigger Type | Action |
| :--- | :--- | :--- |
| **New Release** | Developer Action | Push Tag/Commit to `automations-repo`. |
| **Scheduled Sync** | Automated | Dependabot runs daily per `.github/dependabot.yml`. |
| **Urgent Patch** | Manual | Release Manager clicks "Check for updates" in Subscriber repos. |

## 💡 Best Practice
After merging a major version (e.g., `v2.0.0`) in the central repo, perform a **Manual Trigger** on core application repositories to ensure they are updated within the same maintenance window.

# 🏛️ The "Immutable Release" Architecture

In professional DevOps, we separate **Human Intent** from **Machine Execution**.

| Layer | Component | Purpose |
| :--- | :--- | :--- |
| **Human Intent** | Git Tags (`v1.2.0`) | Communicates stability and features to the team. |
| **Machine Execution** | Commit SHA (`a1b2c3d...`) | Ensures the exact same bits run every time. |
| **The Bridge** | Dependabot | Automatically maps the human Tag to the machine SHA. |

## 🛠️ Typical Professional Flow
1. **DevOps Engineer:** Pushes a fix and tags it `v1.0.1`.
2. **Platform:** Dependabot detects the tag and finds the associated SHA.
3. **App Repository:** Receives a PR to update the SHA.
4. **App Owner:** Merges the PR, knowing exactly what changed via the tag's release notes.