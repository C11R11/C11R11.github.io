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

## 🛡️ Best Practices for the Release Manager
1. **Third-Party Actions:** (e.g., `actions/checkout`) -> **Pin to SHA** if you are in a high-security environment (FinTech, Healthcare).
2. **Internal Automation:** -> **Pin to Major Tag (`@v1`)**. This allows the DevOps team to push critical bug fixes (Patch/Minor) without breaking every caller repo.
3. **External Organization Workflows:** If you don't own the other org, treat it like a Third-Party Action and **Pin to SHA**.

