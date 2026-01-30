[🏡 Home](index.md)

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
```