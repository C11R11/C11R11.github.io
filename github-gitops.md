# 🏗️ The Full GitOps Pipeline Architecture

### Phase 1: The Governance Gate (Entry)
* **Action:** Developer creates a **Pull Request (PR)** from `feature` to `main`.
* **Branch Protection Enforcement:**
    * **Locked State:** The PR cannot be merged by default.
    * **Reviewers:** Requires at least `1` manual approval from a peer with write access.
    * **Required Status Checks:** The "Merge" button is disabled until the **CI Workflow** sends a "Success" signal.

---

### Phase 2: Continuous Integration (CI) Workflow
* **Trigger:** Automatic run on every commit pushed to the PR.
* **The Steps:**
    1.  **Build:** Install dependencies and compile code.
    2.  **Test:** Execute Unit and Integration tests.
    3.  **Security Gate:** Run SAST (SonarQube) and SCA (Dependency Check).
    4.  **Containerize:** Build the **Docker Image** and push to a registry (e.g., Amazon ECR).
* **❌ If CI Fails:** The **Status Check** turns red on the PR. The Branch Protection rule blocks the merge until a fix is pushed.

---

### Phase 3: The Promotion Gate (Handover)
* **Action:** PR is approved and **Merged** into `main`.
* **The Event:** This "Push to Main" triggers the **CD (Continuous Deployment)** workflow.
* **Traceability:** The Git SHA of the merge commit becomes the unique identifier for this deployment.

---

### Phase 4: Environment Security (The Gate)
* **Trigger:** The workflow encounters the `environment: production` definition in the YAML.
* **The Pause:** The job stops immediately and enters a "Waiting" state.
* **Environment Rule Protections:**
    1.  **Wait Timer:** (Optional) A forced cooldown period (e.g., 10 minutes) starts.
    2.  **Approval Gate:** An "Approval Required" notification is sent to the designated environment reviewers.
* **Authorization:** The deployment stays pending until the reviewer manually clicks **"Approve and Deploy."**

---

### Phase 5: Deployment Execution (CLI)
* **Trigger:** Manual Approval is granted (and Wait Timer expires).
* **Execution via CLI:** The runner executes commands to the cloud provider (AWS/K8s/Server).
    * **ECS Deployment:** `aws ecs update-service --cluster my-cluster --service my-app --force-new-deployment`
    * **Kubernetes Deployment:** `kubectl set image deployment/my-app my-container=my-registry/my-image:SHA`
* **Outcome:** The orchestrator performs a **Rolling Update**, ensuring zero downtime while replacing old containers with the new verified image.

---

### 🎓 Architecture Summary Table
| Layer | Tool | Purpose |
| :--- | :--- | :--- |
| **Source** | Git / PR | Captures the "Intent" of a change. |
| **Validation** | GitHub Actions (CI) | Proves the code is safe and healthy. |
| **Governance** | Branch Protection | Ensures human oversight of code. |
| **Deployment Gate** | Environment Rules | Ensures human authorization of infra changes. |
| **Execution** | AWS CLI / Kubectl | Realizes the "Desired State" in the cloud. |