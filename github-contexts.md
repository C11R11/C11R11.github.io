
[Syntax reference](https://docs.github.com/en/actions/learn-github-actions/expressions)
[Context Availability](ttps://docs.github.com/en/actions/learn-github-actions/contexts%23context-availability)

## GitHub Actions Contexts Quick Reference

| Context | Purpose | Common Properties |
| :--- | :--- | :--- |
| **`github`** | Information about the workflow run and the event that triggered it. | `github.ref`, `github.repository`, `github.actor`, `github.event` |
| **`env`** | Variables defined at the workflow, job, or step level using the `env:` key. | `env.MY_VAR` |
| **`vars`** | Configuration variables defined at the Org, Repo, or Environment level. | `vars.API_URL`, `vars.PORT` |
| **`secrets`** | Sensitive data (tokens, passwords) defined in GitHub Settings. | `secrets.GITHUB_TOKEN`, `secrets.DB_PASSWORD` |
| **`inputs`** | Data passed to a `workflow_dispatch` or `workflow_call` event. | `inputs.deploy_target`, `inputs.debug_mode` |
| **`matrix`** | The current job's specific combination of variables in a matrix strategy. | `matrix.os`, `matrix.node-version` |
| **`needs`** | Outputs and status from jobs that this job depends on (via `needs:`). | `needs.build.outputs.version`, `needs.build.result` |
| **`runner`** | Information about the machine executing the current job. | `runner.os`, `runner.temp`, `runner.tool_cache` |
| **`job`** | Metadata about the current job being executed. | `job.status`, `job.container.id` |
| **`steps`** | Status and outputs from steps already executed in the current job. | `steps.step_id.outputs.my_output`, `steps.step_id.outcome` |
| **`strategy`** | Information about the matrix execution strategy. | `strategy.job-index`, `strategy.job-total` |

**Github Context**

[Contexts reference](https://docs.github.com/en/actions/reference/workflows-and-actions/contexts#available-contexts)

[github context](https://docs.github.com/en/actions/reference/workflows-and-actions/contexts#github-context)

[events that triggered workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows)

```text
# Quick info
github.ref -> branch (refs/heads/main)
github.repository -> the owner/repo (cert-labs-hq/automations-repo)
github.action -> the user who triggers the workflow (if it's a re-run the github.triggerent_action may be different)
github.event_name -> The name of the event that triggered the workflow run. (workflow_dispatch)
github.event -> The full event webhook payload.
github.event.workflow -> the workflow yml path (.github/workflows/test-version.yml)
github.workflow_ref -> full workflow path (cert-labs-hq/automations-repo/.github/workflows/test-version.yml@refs/heads/main)
```
