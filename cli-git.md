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


```