# Typo in the runner name

```text
Evaluating test-action.if
Evaluating: success()
Result: true
Requested labels: ubuntu-latest# Mandatory for local actions
Job defined at: cert-labs-hq/automations-repo/.github/workflows/use-repo-action.yml@refs/heads/main
Waiting for a runner to pick up this job...
````

## Explain

Even an AI pass that typo. There's a "#" next o ubuntu latest, that was the main problem. In this state github tries to find that "ubuntu-latest# Mandatory for local actions" runner type, and gets hang for a while.