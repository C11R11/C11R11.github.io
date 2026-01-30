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

```