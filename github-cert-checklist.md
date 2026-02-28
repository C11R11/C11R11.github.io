# GH-200 Study Guide: Domain 1 Checklist (with Code Anchors)

## 1. Author and Manage Workflows (20–25%)

### Configure workflow triggers and events
- [ ] **Configure workflows to run for scheduled, manual, webhook, and repository events** (`on: schedule: - cron:`, `on: workflow_dispatch:`, `on: push:`, `on: pull_request:`)
- [ ] **Choose appropriate scope, permissions, and events for workflow automation** (`permissions: contents: read`, `pull_request` vs `pull_request_target`)
- [ ] **Define and validate workflow_dispatch inputs (types, required, defaults) and pass inputs to reusable workflows via workflow_call with inputs and secrets mapping** (`inputs: name: type: string, default: 'val'`, `with: arg: ${{ inputs.name }}`, `secrets: inherit`)

> GITHUB_TOKEN: Github token created automatically for be used on a workflow run. The token is only for the repo that contains the workflow, and expires when the workflows ends. Can be called with {{secrets.GITHUB_TOKEN}} or {{github.token}}. Can be passed in the with for an action, or in a env in a step. It's more secure to use this token instead of a fine-graned or classic PAT, because it expires.

[how to use the GITHUB_TOKEN to authenticate on behalf of GitHub Actions.](https://docs.github.com/en/actions/tutorials/authenticate-with-github_token#permissions-for-the-github_token)

> The GITHUB_TOKEN permissions can be changes to be more restrictive by an admin

> Also can be grant or deny granually, this is by adding the "permissions" tag on the workflow. All the permission define here will be granted, and all not mention will be denied. The permissions can be added globally, and again in a step or job level.

> If you move your workflow to a different organization that has the "Restricted" default, your workflow will break unless you have explicitly requested the permissions it needs.

```sh
# This sets permissions for EVERY job in the workflow
permissions:
  contents: write    # Allow pushing code/creating tags
  pull-requests: read # Allow reading PR data
  issues: write      # Allow commenting on issues


$gh auth status                                                                                                               
github.com
  ✓ Logged in to github.com account C11R11 (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token: gho_************************************
  - Token scopes: 'gist', 'read:org', 'repo', 'workflow'

```

### Design and implement workflow structure
- [ ] **Use jobs, steps, and conditional logic** (`jobs:`, `steps:`, `if: github.event_name == 'push'`)
- [ ] **Implement dependencies between jobs** (`needs: [job_a, job_b]`)
- [ ] **Use workflow commands and environment variables** (`echo "VAR=VAL" >> $GITHUB_ENV`, `::error::message`)
- [ ] **Use service containers (services:) for dependent services (databases, queues); configure ports, health checks, and container options** (`services: redis: image: redis`, `ports: ["6379:6379"]`, `options: "--health-cmd ..."`)
- [ ] **Use strategy and matrix to generate job variations (OS, language/runtime versions); apply include/exclude; control fail-fast and max-parallel; optimize matrix size for cost and performance; account for runner image changes (Ubuntu 20.04 deprecation, Windows Server 2025 migration for windows-latest)** (`strategy: matrix: node: [18, 20]`, `include:`, `exclude:`, `fail-fast: false`, `runs-on: windows-2025`)
- [ ] **Implement YAML anchors and aliases (&, * and merge <<) to reuse repeated mappings/steps within a single workflow file** (`env: &defaults`, `env: *defaults`, `<<: *defaults`)
- [ ] **Use predefined contexts (github, runner, env, vars, secrets, inputs, matrix, needs, strategy, job, steps, github.event, github.ref) to access workflow, repository, and runtime metadata; understand immutable actions behavior and version pinning requirements** (`${{ github.sha }}`, `${{ secrets.KEY }}`, `uses: actions/checkout@v4` vs `@SHA`)
- [ ] **Evaluate expressions with ${{ }} referencing contexts; distinguish static (workflow parse) vs runtime evaluation; prevent secret leakage in logs and expressions** (`if: ${{ github.event_name == 'push' }}`, `env: PASSWORD: ${{ secrets.PW }}`)
- [ ] **Leverage editor tooling (GitHub Actions VS Code extension / YAML schema completion, metadata IntelliSense, validation) to author and maintain workflows efficiently** (Action/YAML linting extension in VS Code)

### Manage workflow execution and outputs
- [ ] **Configure caching and artifact management; apply retention policies via REST APIs (logs, artifacts, workflow runs) at org/repo level** (`uses: actions/cache@v4`, `uses: actions/upload-artifact@v4`, `retention-days: 30`)
- [ ] **Pass data between jobs and steps (artifacts, outputs, environment files via GITHUB_ENV and GITHUB_OUTPUT, reusable workflow outputs)** (`echo "val=foo" >> $GITHUB_OUTPUT`, `outputs: my_out: ${{ steps.id.outputs.val }}`)
- [ ] **Generate job summaries using GITHUB_STEP_SUMMARY for rich Markdown reports (test results, coverage, links)** (`echo "### Test Passed" >> $GITHUB_STEP_SUMMARY`)
- [ ] **Add workflow status badges and environment protections** (`![Badge](https://github.com/...)`, `environment: production`)


## 2. Consume and troubleshoot workflows (15–20%)

### Interpret workflow behavior and results
- [ ] **Identify workflow triggers and effects from configuration and logs** (Check `github.event_name` in logs / UI "Triggered by" label)
- [ ] **Diagnose failed workflow runs using logs and run history** (Check red ❌ status, "Annotations" tab, or `gh run view --log`)
- [ ] **Expand and interpret YAML anchors, aliases, and merged mappings when analyzing workflow configuration** (`&anchor_name`, `*alias_reference`, `<<: *merge_map`)
- [ ] **Interpret matrix expansions, correlate job names to matrix axes, analyze failures across variants, and selectively rerun individual matrix jobs** (UI: "Jobs" sidebar list; "Re-run failed jobs" button)

### Access workflow artifacts and logs
- [ ] **Locate workflows, logs, and artifacts in the UI and via API** (`GET /repos/{owner}/{repo}/actions/runs`, `gh run list`)
- [ ] **Download and manage workflow artifacts** (`uses: actions/download-artifact@v4`, `gh run download <run_id>`)

### Use and manage workflow templates
- [ ] **Consume organization-level and reusable workflows** (`uses: <org>/<repo>/.github/workflows/<file>.yml@<ref>`)
- [ ] **Consume non-public organization workflow templates** (Settings -> Actions -> General -> "Allow all actions and reusable workflows")
- [ ] **Use starter workflows (public and private/non-public templates); customize and adapt; distinguish from reusable workflows and composite actions** (`.github/workflow-templates/*.yml` in the `.github` repo)
- [ ] **Differentiate starter workflows (copy scaffold, independent after creation) vs reusable workflows (central versioned definition invoked via workflow_call) vs composite actions (encapsulated step logic)** (Copy/Paste UI gallery vs `workflow_call` vs `runs: using: 'composite'`)
- [ ] **Contrast disabling and deleting workflows** (Actions -> "..." menu -> "Disable workflow" vs "Delete workflow run")

## 3. Author and maintain actions (15–20%)

### Create and troubleshoot custom actions
- [ ] **Identify and implement action types (JavaScript, Docker, composite); understand immutable actions rollout on hosted runners and implications for version pinning and registry sources** (`using: 'node20'`, `using: 'docker'`, `using: 'composite'`; `uses: actions/checkout@v4` vs `@SHA`)
- [ ] **Troubleshoot action execution and errors** (Inspect `dist/` for JS actions, check `ENTRYPOINT` for Docker, and shell context for Composite)

> * In a Composite Action, the shell: bash line is mandatory for every run step. In a standard Workflow job, it is optional (it defaults to bash on Linux), but the GH-200 exam loves to trick you by omitting it in a Composite Action.

### Define action structure and metadata
- [ ] **Specify required files, directory structure, and metadata** (`action.yml` file, `inputs:`, `outputs:`, `runs:`)
- [ ] **Implement workflow commands within actions** (JS: `core.setFailed()`, `core.setOutput()`; Bash: `echo "::set-output name=x::y"`)

### Distribute and maintain actions
- [ ] **Select distribution models (public, private, marketplace)** (Public repo for marketplace vs. Internal/Private repo for Org-only)
- [ ] **Publish actions to the GitHub Marketplace** (`branding: icon: 'check-circle', color: 'blue'`)
- [ ] **Apply versioning and release strategies** (`git tag -a v1.0.0`, `git tag -fa v1 -m "Update v1 tag"`)

## 4. Manage GitHub Actions for the enterprise (20–25%)

### Distribute and govern actions and workflows
- [ ] **Define and manage reusable components and templates** (`on: workflow_call:`, `.github/workflow-templates/`)
- [ ] **Control access to actions and workflows within the enterprise** (Enterprise Settings -> Policies -> Actions -> "Allowed actions")
- [ ] **Configure organizational use policies** (Settings -> Actions -> General -> "Allow all", "Allow select actions", "Allow verified creators")

### Manage runners at scale
- [ ] **Configure and monitor GitHub-hosted and self-hosted runners** (`runs-on: ubuntu-latest` vs `./config.sh --url ... --token ...`)
- [ ] **Apply IP allow lists and networking settings** (Enterprise Settings -> "IP allow list" for Runner API/Actions traffic)
- [ ] **Manage runner groups and troubleshoot runner issues** (Settings -> Actions -> Runner Groups -> "Selected repositories"; check `_diag` folder for logs)
- [ ] **Identify preinstalled software/tool versions on GitHub-hosted runners (image release notes, toolcache) and install additional software at runtime (setup-* actions, package managers, caching, container images, custom self-hosted images)** (Check `runner-images` repo, `uses: actions/setup-node@v4`, `sudo apt-get install`, `uses: actions/cache@v4`)

### Manage encrypted secrets and variables
- [ ] **Define and scope encrypted secrets and variables at the organization, repository, and environment levels** (Settings -> Secrets and variables -> Actions; UI scoping options)
- [ ] **Access and use secrets and variables in workflows and actions; manage secrets and variables programmatically via REST APIs** (`${{ secrets.ORG_SECRET }}`, `${{ vars.GLOBAL_VAR }}`, `gh secret set <name> --org <org_name>`)

## 5. Secure and optimize automation (10–15%)

### Implement security best practices
- [ ] **Use environment protections and approval gates** (`environment: production`, "Required reviewers", "Wait timer")
- [ ] **Identify and use trustworthy actions from the Marketplace** (Look for the "Verified Creator" blue badge ✅)
- [ ] **Mitigate script injection (sanitize/validate inputs, least-privilege permissions, avoid untrusted data in run:, proper shell quoting, prefer vetted actions over inline scripts)** (Use `env` variables for `${{ github.event... }}` instead of direct expansion in `run:`)
- [ ] **Understand GITHUB_TOKEN lifecycle (ephemeral, scoped), configure granular permissions, contrast with PAT; restrict write scopes** (`permissions: contents: read`, `pull-requests: write`)

> * Token Scoping: Fine-grained PATs must have the Organization as the Resource Owner to modify Org repos; otherwise, you get the 403 you encountered.
> * The "Metadata" Base: Every token needs at least metadata: read to even see a repository's exists via the API.
> * To create repos you need the Administration and Content permission (read and write)

- [ ] **Use OIDC token (id-token permission) for cloud provider federation to eliminate long-lived cloud secrets** (`permissions: id-token: write`, `uses: aws-actions/configure-aws-credentials@v4`)
- [ ] **Pin third-party actions to full commit SHAs; align with immutable actions enforcement on hosted runners; avoid floating @main/@v* without justification** (`uses: actions/checkout@a5ac7e51b41094c92402da3b24376905380afc29`)
- [ ] **Enforce action usage policies (organization/repository allow/deny lists, required reviewers for unverified actions)** (Org Settings -> Actions -> General -> "Allow select actions")
- [ ] **Generate and verify artifact attestations / provenance (e.g., SLSA, build metadata) and integrate into deployment verification** (`uses: actions/attest-build-provenance@v1`, `gh attestation verify`)

### Optimize workflow performance and cost
- [ ] **Configure caching and artifact retention for efficiency; apply retention policies programmatically via REST APIs** (`uses: actions/cache@v4`, `retention-days: 5`, `DELETE /repos/{owner}/{repo}/actions/artifacts/{artifact_id}`)
- [ ] **Recommend strategies for scaling and optimizing workflows** (Parallelize via `matrix`, use `concurrency` groups, minimize `fetch-depth: 0` in checkout)