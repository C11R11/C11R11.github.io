[Strategy Context Reference](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idstrategy)

[Running variations of jobs in a workflow](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/run-job-variations)

### 🧬 GitHub Actions Matrix Strategy Reference

The `strategy` block allows you to run a single job definition across multiple variations simultaneously.

| Property | Purpose | Exam Focus |
| :--- | :--- | :--- |
| **`matrix`** | Defines the variables and their values. | Every combination (Cartesian product) is created by default. |
| **`fail-fast`** | Determines if GitHub should cancel all in-progress jobs if one fails. | Default is `true`. Set to `false` for exhaustive testing. |
| **`max-parallel`** | Limits the number of jobs running at the same time. | Use this to avoid hitting API rate limits or runner quotas. |
| **`include`** | Adds specific combinations or extra variables to a matrix. | Can add a unique variable (like a specific `flag`) to just one row. |
| **`exclude`** | Removes specific combinations from the matrix. | Essential for removing unsupported versions (e.g., Node 20 on Windows 2019). |

| Feature | Syntax | Impact |
| :--- | :--- | :--- |
| **Expansion** | `matrix: { os: [A, B], node: [1, 2] }` | Creates 4 jobs (Ax1, Ax2, Bx1, Bx2). |
| **Include** | `include: [{ os: A, flag: '--fast' }]` | Adds `matrix.flag` ONLY to Ax1 and Ax2. |
| **Exclude** | `exclude: [{ os: B, node: 1 }]` | Removes the Bx1 job from the run. |
| **Fail-Fast** | `fail-fast: false` | Forces all 4 jobs to finish even if 3 fail. |

---

> The matrix context is only available inside a job that has a strategy: matrix: defined. You cannot use ${{ matrix.anything }} in a different job unless that job also has its own matrix.

> If you use ${{ matrix.os }} in the runs-on field, GitHub will "multiply" that job. If your matrix has 2 OSs and 3 Versions, GitHub creates 6 distinct jobs.

### 🧬 Matrix Variable Naming Rules

| Component | Rule | Example |
| :--- | :--- | :--- |
| **Context Name** | **Reserved.** Must be `matrix`. | `${{ matrix.version }}` |
| **Property Keys** | **Custom.** Use any alphanumeric name. | `os:`, `runtime:`, `database:` |
| **Property Values** | **Arrays.** Must be a list of values. | `[18, 20]` or `['latest', 'beta']` |
| **Accessing Values** | **Expression Syntax.** Use `${{ }}`. | `runs-on: ${{ matrix.os }}` |

#### ⚠️ Warning: Reserved Key Names
Avoid using keys that match top-level YAML properties like `include` or `exclude` as your variable names, as this will confuse the YAML parser.
* **Bad:** `matrix: { include: [v1, v2] }`
* **Good:** `matrix: { version: [v1, v2] }`

### 🚨 Critical Update: Runner Image Lifecycles

As a DevOps Engineer, you must track image deprecations to prevent "Pipeline Rot".

* **Ubuntu-latest**: Transitions from **22.04** to **24.04** periodically. Be aware that `ubuntu-20.04` is reaching End-of-Life.
* **Windows-latest**: In 2026, this typically points to **Windows Server 2025**. If your code relies on older drivers, you must pin to `windows-2022`.
* **The "Pinned" Rule**: For production reliability, always prefer `ubuntu-22.04` over `ubuntu-latest` to avoid surprise breaks during image migrations.

- [x] **Use predefined contexts (github, runner, env, vars, secrets, inputs, matrix, needs, strategy, job, steps, github.event, github.ref) to access workflow, repository, and runtime metadata; understand immutable actions behavior and version pinning requirements** (`${{ github.sha }}`, `${{ secrets.KEY }}`, `uses: actions/checkout@v4` vs `@SHA`)