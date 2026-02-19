# GH-200: JavaScript vs. Docker vs. Bash (Composite) Actions

In the certification exam, you will likely be asked to choose the "Best Action Type" for a specific scenario. Use this comparison as your guide.

# CI and CD over workflows and reusable components

## 1. The "CI" vs. "CD" Divide
Your current workflow is **Linear**:
`Checkout` -> `Test` -> `Scan` -> `Build` -> `Upload`.

Bash is perfect for this because these are mostly CLI-driven tasks. You don't need complex state management or high-level API interaction for these.

## 2. Where the "Native Action" (JS/Docker) Benefits Start
You start seeing the benefit of JavaScript/Docker actions when you hit these three "Enterprise" scenarios:

### A. Complex "Stateful" Interactions
* **Scenario:** You want to check if a specific Jira ticket is approved before allowing a deployment.
* **Bash way:** 50 lines of `curl`, `jq`, and complex `if/else` logic to handle Jira's API.
* **Action way:** Use a JS Action with a Jira SDK. It handles retries, authentication, and error types natively.

### B. Deployment "Post-Processing"
* **Scenario:** You deploy a Docker image to ECR, but if the smoke test fails, you want to **automatically rollback** or notify a Slack channel with a specific button.
* **The "Post" Step:** Only Native Actions (JS/Docker) have a `post:` execution. This allows an action to "clean up" or "react" after the job finishes, regardless of success or failure. Bash scripts in a workflow can't do this natively without `if: always()` blocks everywhere.

### C. Creating a "Paved Road" for other Devs
* **The Problem:** If you give 100 developers a Bash script to use, they might edit it, break the logic, or use the wrong version.
* **The Solution:** A **Versioned Action**. By moving your SAST/SCA logic into a JS Action, you can force everyone to use `uses: your-org/sast-action@v2`. You can update the logic behind the scenes without the developers ever touching a line of YAML.

---

## 3. What you need to "Visualize" for the Exam
Since you haven't implemented **Environments**, you must memorize these three things for the GH-200, as they are "Environment-Only" features:

1. **Deployment Branch Policies:** You can't do this in a workflow script. It’s a UI/API setting that says: "Only the `main` branch is allowed to trigger a job that uses the `production` environment."
2. **Environment Secrets:** These are secrets that **only exist** when an environment is active.
   * *Exam Scenario:* You have a `DB_PASSWORD` for Staging and one for Production. In your YAML, you just use `${{ secrets.DB_PASSWORD }}`. GitHub knows which one to provide based on the `environment:` name.
3. **Protection Rules (The "Gate"):** This is the biggest gap in your practice. You need to know that a job can "wait" for a human to click a button in the GitHub UI before the script even starts.

---

### Summary for your MD notes:
| Stage | Best Tool | Why? |
| :--- | :--- | :--- |
| **Local CI** (Build/Test) | Bash / Composite | Fast, direct access to CLI tools. |
| **Enterprise CD** (Deploy/Gates) | JS / Docker Actions | Better API handling, `post` cleanup, versioning. |
| **Compliance** | Environments | Reviewers, Wait Timers, Branch restrictions. |


# Types and uses

## 1. JavaScript Actions (`using: node20`)
The "Gold Standard" for the GitHub Marketplace.
* **Speed:** Faster than Docker because there is no container image to pull or build.
* **Cross-Platform:** Runs natively on **Linux, macOS, and Windows** runners.
* **Tooling:** You get access to the `@actions/toolkit`, which makes interacting with the GitHub API, secrets, and logs much easier than parsing strings in Bash.
* **Best For:** Most general-purpose actions (e.g., `actions/checkout`, `actions/setup-node`).

## 2. Docker Container Actions (`using: docker`)
The "Environment King."
* **Consistency:** You package the exact OS (e.g., Alpine, Debian), specific binaries, and dependencies. If it works on your machine, it works on the runner.
* **Language Agnostic:** You can write the logic in **Python, Go, Rust**, or even complex Bash scripts that require specific CLI tools not present on standard runners.
* **Major Limit:** They **only run on Linux** runners. They will fail on Windows or macOS.
* **Best For:** Complex tools with heavy dependencies (e.g., a specific version of Terraform or a security scanner).

## 3. Composite Actions (Raw Bash / `using: composite`)
The "Script Wrapper."
* **Simplicity:** No need to learn Node.js or Docker. Just wrap your existing `.sh` or `.ps1` scripts.
* **Performance:** Very fast startup (no container build).
* **Limitations:** * Harder to handle complex logic or error states.
    * You are stuck with whatever is pre-installed on the runner.
    * Testing is much harder than unit-testing JavaScript.
* **Best For:** Small, internal scripts that you just want to "DRY" up across 2-3 repos.

---

## Comparison Summary Table

| Feature | JavaScript | Docker | Composite (Bash) |
| :--- | :--- | :--- | :--- |
| **Startup Speed** | 🚀 Fastest | 🐢 Slow (Pull/Build) | 🚀 Fastest |
| **Platform** | Linux, Win, macOS | **Linux Only** | Linux, Win, macOS |
| **Isolation** | Shared with Runner | **Total (Container)** | Shared with Runner |
| **Maintenance** | Low (NPM) | High (Dockerfiles) | Low |
| **API Access** | Rich Toolkit | Manual (curl/CLI) | Manual (curl/CLI) |

## Exam "Catch" Questions
* **"Which action type is best for a tool that requires a specific version of an Alpine Linux library?"** -> **Docker**.
* **"Which action type supports all GitHub-hosted runner operating systems?"** -> **JavaScript** or **Composite**.
* **"Which action type is the fastest to execute for a simple API call?"** -> **JavaScript**.

# Why Move from Bash/Workflows to Actions?

If you are already comfortable with Bash and `composite` workflows, here is why GitHub (and the exam) pushes you toward "Native Actions" (JS/Docker).

## 1. The "Toolkit" Advantage (No more `curl` hell)
In Bash, if you want to comment on a PR or check a label, you have to write `curl` commands against the GitHub API, handle JSON parsing with `jq`, and manage Auth headers.
* **In a JS Action:** You use the `@actions/github` library. It’s a pre-authenticated SDK.
* **Benefit:** Your code is 90% logic and 10% "plumbing," instead of 90% Bash boilerplate.

## 2. Cleaner UI and Logging
If a Bash script fails in a Composite Workflow, the UI often just shows a big red block of text.
* **Problem Matchers:** JS/Docker Actions allow you to use "Problem Matchers." This highlights the *exact line of code* in your repo that caused the error, right in the PR "Files Changed" tab.
* **Grouped Logs:** You can programmatically start and end log groups, making the Actions console much easier to debug for other developers.

## 3. Input Validation and Types
In a workflow, your "inputs" are just strings. If a dev passes `false` (the string) instead of `false` (the boolean), your Bash script might break.
* **JS Actions:** Can perform complex validation, set default values, and handle complex objects before any logic runs.

## 4. State Management (`Post` Steps)
This is a **major exam topic**. Bash scripts in a workflow run and then finish.
* **The "Cleanup" Problem:** If you start a Docker container or an SSH tunnel in a script, you have to remember to kill it at the end of the workflow.
* **Actions "Post" Feature:** JS and Docker actions support a `post:` execution. This is code that runs **automatically at the end of the job**, even if the job fails. 
* **Example:** The `actions/checkout` action uses this to clean up credentials.

## 5. Versioning and Distribution
* **Workflows:** Are usually tied to the repo they live in or called via a long file path.
* **Actions:** Are versioned units of software. You can release `v1.0.0`, `v1.1.0`, etc. Users can stay on `v1` to get bug fixes without breaking changes. This is much harder to manage with raw `.yml` files.

---

### Comparison: The "Developer Experience"

| Scenario | Raw Bash / Composite | JavaScript / Docker Action |
| :--- | :--- | :--- |
| **Parsing JSON** | Requires `jq` (heavy syntax). | Native JSON objects. |
| **API Calls** | Manual `curl` + Token handling. | Native SDK (`octokit`). |
| **Error Handling** | `set -e` (blunt instrument). | `try/catch` + specific error codes. |
| **Cleanup** | Manual "Always" steps. | Automatic `post` execution. |
| **Testing** | Hard (ShellCheck/BATS). | Standard Unit Testing (Jest/Mocha). |

---

## Final Exam "Key Concept"
When the exam asks about **"Scaling a DevOps practice,"** the answer is almost always to move away from "local scripts" (Bash) and toward **"Custom Actions" (JS/Docker)**. This allows you to treat your CI/CD components like actual software with versions, tests, and clear interfaces.

# Anatomy of a Docker Action

Unlike a simple script, a Docker Action bundles the environment with the code. Here are the three files you need to create.

## 1. The Metadata File (`action.yml`)
This defines the interface. It tells GitHub this is a Docker action and which image to use.

```yaml
name: 'Custom Bash Reporter'
description: 'Runs a complex bash script in a controlled environment'
inputs:
  user_name:
    description: 'Who to greet'
    required: true
    default: 'GitHub User'
outputs:
  time:
    description: 'The time the greeting happened'
runs:
  using: 'docker'
  image: 'Dockerfile' # GitHub will build this on the fly
  args:
    - ${{ inputs.user_name }} # Passes input as a CLI argument
```

## 2. The Environment File (`Dockerfile`)
This defines the "Runner" requirements. You can install any tool here (like `curl`, `jq`, or `aws-cli`).

```dockerfile
# Use a lightweight base
FROM alpine:3.18

# Install dependencies your script needs
RUN apk add --no-cache bash curl

# Copy the script from your repo into the container
COPY entrypoint.sh /entrypoint.sh

# Make the script executable
RUN chmod +x /entrypoint.sh

# GitHub will run this script when the action starts
ENTRYPOINT ["/entrypoint.sh"]
```

## 3. The Logic File (`entrypoint.sh`)
This is where your existing Bash experience lives. 

```bash
#!/bin/bash
set -e

# Access inputs passed via 'args' in action.yml
NAME=$1

echo "Hello $NAME!"

# Example of a Workflow Command (Exam Topic!)
current_time=$(date)
echo "time=$current_time" >> $GITHUB_OUTPUT

echo "Action completed successfully."
```

---

## 3 Benefits for your Certification

### 1. Zero Dependency Management
On a standard runner, you might need a specific version of a tool. With a Docker Action, you don't care what is on the runner; you provide the tools inside the `Dockerfile`.

### 2. Standardized Inputs/Outputs
Instead of your script looking for random environment variables, the `action.yml` enforces a contract. This makes your automation "Self-Documenting."

### 3. Filesystem Isolation
The action runs inside the container, but GitHub automatically mounts the **workspace** (`GITHUB_WORKSPACE`) into the container. 
* **Exam Tip:** If you create a file in `/github/workspace` inside the container, it persists for the next step in the workflow.

---

## The "Docker Action" Exam Checklist
* [ ] **Linux Only:** Does this need to run on Windows? If yes, you **cannot** use Docker; you must use JavaScript.
* [ ] **Speed:** Docker actions are slower because GitHub has to build or pull the image every time.
* [ ] **Versioning:** You can point `image:` to a pre-built image on **GitHub Packages** or **Docker Hub** to speed it up.

