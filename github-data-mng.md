# 📊 Data Management: Artifacts vs. Cache vs. Outputs

In GitHub Actions, managing data efficiently depends on understanding where the data needs to go and how long it needs to live.

| Feature | Scope | Persistence | Best Use Case |
| :--- | :--- | :--- | :--- |
| **Outputs** | **Job-to-Job** | Current Run Only | Passing small strings, IDs, version numbers, or boolean flags between dependent jobs. |
| **Artifacts** | **Workflow-to-User** | Up to 90 Days | Storing build binaries (.exe, .apk), test coverage reports, or logs for human download. |
| **Cache** | **Run-to-Run** | Up to 7 Days* | Speeding up builds by reusing expensive dependencies like `node_modules`, `~/.nuget/packages`, or `~/.m2`. |

## 🔑 Key Strategic Differences

### 1. Direction of Flow
* **Outputs:** Move **forward** in time within a single workflow execution (Job A → Job B).
* **Artifacts:** Move **outward** to the user or an external storage area.
* **Cache:** Moves **backward** from a past successful run into a future run.

### 2. Storage & Costs
* **Artifacts:** Count against your GitHub storage quota (once the limit is reached, you are billed).
* **Cache:** GitHub evicts the oldest cache once you hit a **10GB limit per repository**. It does not cost extra money but is "best effort" storage.

### 3. The "Bust" Mechanism
* **Outputs/Artifacts:** These are fresh for every run; no "busting" required.
* **Cache:** Requires a change in the `key` to invalidate. If your `package-lock.json` or `csproj` hasn't changed, the cache will persist. 

---
*Note: Caches are automatically deleted if they haven't been accessed in 7 days.*