[back](cli-index)

# Basics

```sh
# Run a workflow with parameters
gh workflow run <workflow-name>.yml -f <param1-name>=<param1-value> -f <param1-name>=<param1-value> --ref <repo>

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

# Set the PAT
export GITHUB_TOKEN=<token-here>
export GITHUB_OWNER=<owner-here>

# A fine grained PAT must be defined instead of classic. For example
# to be able to create teams with terraform, it's needed to add a nde
# fine-grained token with Members permision (Organization members and teams)
```