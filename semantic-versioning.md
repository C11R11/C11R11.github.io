# 🔢 Semantic Versioning (SemVer) Guide

Semantic Versioning is a formal specification for a proposed versioning scheme that communicates the **underlying changes** in a release through its version number.

## 🏗️ The Structure: `MAJOR.MINOR.PATCH`

A version number is formatted as `X.Y.Z` (e.g., `v2.1.4`). Each digit has a strict meaning:

### 1. MAJOR (X) - "The Breaking Change"
* **When to increment:** When you make incompatible API changes.
* **Impact:** Users **must** change their code to upgrade. 
* **Rule:** When MAJOR is incremented, MINOR and PATCH must reset to `0`.
* *Example:* `1.8.2` → `2.0.0`

### 2. MINOR (Y) - "The Feature Addition"
* **When to increment:** When you add functionality in a backward-compatible manner.
* **Impact:** Users get new features but don't have to change their existing code.
* **Rule:** When MINOR is incremented, PATCH must reset to `0`.
* *Example:* `1.8.2` → `1.9.0`

### 3. PATCH (Z) - "The Bug Fix"
* **When to increment:** When you make backward-compatible bug fixes.
* **Impact:** Users get a more stable version with no new features or breaking changes.
* *Example:* `1.8.2` → `1.8.3`

---

## 🛠️ The Release Manager's Workflow

### 1. The Pre-Release Labels
Before a final version is ready, use "tags" to signal the state of the code:
* `1.0.0-alpha.1` : Internal testing, very unstable.
* `1.0.0-beta.1` : Ready for "power users" to find bugs.
* `1.0.0-rc.1` : (Release Candidate) Final version if no major bugs appear.

### 2. The Golden Rules
1.  **Immutability**: Once a version is released (tagged), you **never** modify the contents of that version. If you find a typo, you release a new version (Patch).
2.  **Starting at Zero**: Use `v0.x.x` for initial development. This signals that the API is not yet stable.
3.  **The "v" Prefix**: While the spec is just numbers, it is industry standard to prefix with `v` (e.g., `v1.0.0`) in Git tags.

---

## 🚦 Decision Matrix: Which number do I change?

| Did I... | Increment | New Version Example |
| :--- | :--- | :--- |
| Fix a bug or typo? | **PATCH** | `1.0.0` → `1.0.1` |
| Add a new input/feature? | **MINOR** | `1.0.1` → `1.1.0` |
| Remove or rename an input? | **MAJOR** | `1.1.0` → `2.0.0` |
| Update documentation only? | **PATCH** | `2.0.0` → `2.0.1` |

---

## 🎓 DevOps Implementation Tip
When using GitHub Actions, your **Major Version Tag** (e.g., `@v1`) should always point to the **latest** Minor/Patch release of that major version. 

* *User uses:* `uses: my-repo/action@v1`
* *You release:* `v1.0.1` -> Point `@v1` here.
* *You release:* `v1.1.0` -> Point `@v1` here.
* *You release:* `v2.0.0` -> **STOP.** Do not move `@v1`. Create a new `@v2` tag.

# 🛠️ SemVer for Infrastructure & DevOps

In DevOps, the "API" isn't a web endpoint; it is the **Inputs, Outputs, and Environment Variables** of your scripts and modules.

## 🚦 Change Categories for Automation

### 🔵 MAJOR (X.0.0) - Breaking Infrastructure
* **Renaming/Removing** a required `input` in a Reusable Workflow.
* Changing a Terraform variable name.
* Changing the **Runner Type** (e.g., switching from `ubuntu-latest` to a self-hosted Mac Mini) if it requires different software.
* Upgrading a core tool version (e.g., moving from Java 11 to Java 21) that might break builds.

### 🟢 MINOR (0.X.0) - New Capabilities
* Adding an **Optional** `input` (with a default value).
* Adding a new `output` to a workflow.
* Adding a new "Step" that performs a non-breaking check (like a new linter).
* Adding support for a new AWS Region.

### 🟡 PATCH (0.0.X) - Stability & Maintenance
* Fixing a typo in a `run:` script.
* Updating a GitHub Action version (e.g., `actions/checkout@v3` to `@v4`) inside your composite action.
* Optimizing a script to run 10% faster without changing how it's called.
* Updating documentation or README instructions.

---

## 🏛️ The "Internal Platform" Strategy

When you are the "Release Manager" for an automation library:

1.  **Pin to Major Tags**: Tell your developers to use `@v1`.
2.  **Internal Testing**: Always test your `vNext` on a dummy repository before moving the `@v1` tag in the main library.
3.  **Deprecation Warnings**: If you plan a **Major** change, add a "Warning" step in the current **v1** workflow:
    ```bash
    echo "::warning ::Input 'ref_name' is deprecated. Please prepare to move to v2.0.0."
    ```


# 📢 DevOps Communication: The Deprecation Strategy

As a Release Manager for automation/infrastructure, you must bridge the gap between "Code is ready" and "Users are ready."

## 1. The Warning Phase (Current Major Version)
Before releasing a **Breaking Change (v2.0.0)**, add a warning to the **v1.x.x** branch.
* **Goal:** Zero surprises for developers.
* **Implementation:** Use `echo "::warning ::<message>"` in your scripts.

## 2. The Migration Guide
Whenever you release a **Major** version, you must provide a "Migration Guide." 
* **Example:** > **To upgrade from v1 to v2:**
  > 1. Rename `ref_name` to `target_branch`.
  > 2. The `TOP_SECRET` secret is now mandatory.

## 3. The "Sunset" Period
Decide how long you will support the old version.
* **Standard:** Support the previous Major version for 3-6 months after the new one is released.
* **Action:** Periodically check the "Action usage" metrics in GitHub to see who is still pinned to `@v1`.

---

### 🛠️ Professional Workflow Command Syntax

| Command | UI Effect | Best Use Case |
| :--- | :--- | :--- |
| `::warning ::<msg>` | Yellow Box | Deprecations or "Soft" errors. |
| `::error ::<msg>` | Red Box | Critical failures that stop the job. |
| `::notice ::<msg>` | Blue Box | General info (e.g., "Deployment successful"). |
| `::group ::<name>` | Collapsible | Hiding long logs in a clean dropdown. |