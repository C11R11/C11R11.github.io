[Cli index](cli-index) | [Github cert](github-cert.md)   

# Basics

```sh
# Remove a repo without prompt (careful!)
gh repo delete <owner>/<repo> --yes

# List al repos for a specific owner (in case of working within an organization)
gh repo list <owner> --limit 1000

# Search for a repo 
gh search repos <match-word> --owner=<owner>

# Login
gh auth login

# Logout
gh auth logout

# Api meta (provides information about GitHub.com or a GitHub Enterprise installation)
gh api meta

# Set the PAT
export GITHUB_TOKEN=<token-here>
export GITHUB_OWNER=<owner-here>

# A fine grained PAT must be defined instead of classic. For example
# to be able to create teams with terraform, it's needed to add a nde
# fine-grained token with Members permision (Organization members and teams)
```

# Workflow run

```sh
# Run a workflow with parameters
gh workflow run <workflow-name>.yml -f <param1-name>=<param1-value> -f <param1-name>=<param1-value> --ref <repo>

#Allows you to watch the output directly in your terminal.
gh run view RUN_ID --log

# You can also use the --job flag to specify a job ID. Replace job-id with the ID of the job that you want 
# to view logs for.
gh run view --job JOB_ID --log

# chaining
gh run view --job JOB_ID --log | grep error

# To filter the logs for any failed steps, use --log-failed instead of --log.
gh run view --job JOB_ID --log-failed

# See all the runs with more info (and the Id)
gh run list

# Download the artifact from a certain run Id
gh run download <run_id>
```

# Cache

```sh
# List all caches for the current repo
gh cache list

# Delete a specific cache by ID
gh cache delete <cache-id>

# Delete all caches for a specific branch
gh cache delete --branch main
```

# Secrets
```sh
# Set a repository variable (Non-encrypted)
gh variable set LOG_LEVEL --body "debug" --repo cert-labs-hq/external-repo

# Set a repository secret (Encrypted)
gh secret set DEPLOY_KEY --body "super-secret-string" --repo cert-labs-hq/external-repo

# List them to verify
gh secret list --repo cert-labs-hq/external-repo

# For organization secrets, they can define certains repos to be used
gh secret set SECRET_NAME --org your-organization --repos "repository1,repository2,..."
```

# Artifacts

```sh
#Verify Attestation
gh attestation verify <file> --repo <owner/repo>

# List Artifacts
gh run view <run-id> --json artifacts

# Delete Artifact
DELETE /repos/{owner}/{repo}/actions/artifacts/{id}