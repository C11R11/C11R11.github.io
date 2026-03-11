[back](cli-index)

# Basics

```sh
# Repo status and history
git log --oneline --graph --decorate --all

# See an specific commit changes
git show <commit-hash>

# New branch from main (feature)
git switch -c feature/my-new-feature
git push -u origin feature/my-new-feature

# One line for commit 
git add -A; git commit -m "bla"; git push

# New branch with no history
git switch --orphan <branch-name>
git commit --allow-empty -m "Initial empty commit" 

# Know the repository url
git remote -v

# Clone with token 
git clone https://<PAT>@github.com/<ORG>/<repo-name>.git

# What's my git user and email
git config user.name
git config user.email


# Change the name and email
git config --global user.name "name"
git config --global user.email "email"

# Emergency: The remote was erased and recreated but empty. I need to push my local repo
git push --force-with-lease

[Reference](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History)

# List commit history
git log --oneline 

# List commits with full sha (Secure Hash Algorithm)
git log --pretty=oneline

# Get the SHA for a specific Tag
git rev-parse v1.0.0

# Get the latest commit SHA (Current Branch)
git log -n 1 --pretty=format:"%H"
