---
tags:
  - Cheat_Sheet
  - git
---
# 🧠 1. Repository Setup

## Initialize Repository
```bash
git init
```

## Clone Existing Repository (HTTPS)
```bash
git clone https://github.com/user/repo.git
```

## Clone Using SSH
```bash
git clone git@github.com:user/repo.git
```

---

# 📁 2. Basic Daily Workflow

```bash
git status
git add .
git commit -m "Your message"
git pull --rebase
git push
```

---

# 📌 3. Tracking & Staging

## Check Status
```bash
git status
```

## Add Specific File
```bash
git add filename
```

## Add All Files
```bash
git add .
```

## Unstage File
```bash
git restore --staged filename
```

## Discard Changes
```bash
git restore filename
```

---

# 💾 4. Committing

## Commit
```bash
git commit -m "message"
```

## Amend Last Commit
```bash
git commit --amend
```

## Amend Without Changing Message
```bash
git commit --amend --no-edit
```

---

# 🌿 5. Branching

## List Branches
```bash
git branch
```

## Create Branch
```bash
git branch branch-name
```

## Create & Switch (Legacy)
```bash
git checkout -b branch-name
```

## Create & Switch (Modern)
```bash
git switch -c branch-name
```

## Switch Branch
```bash
git switch branch-name
```

## Delete Branch
```bash
git branch -d branch-name
```

## Force Delete Branch
```bash
git branch -D branch-name
```

---

# 🔁 6. Merging

## Merge Into Current Branch
```bash
git merge branch-name
```

## Abort Merge
```bash
git merge --abort
```

---

# 🔄 7. Rebasing

## Rebase Current Branch Onto Main
```bash
git rebase main
```

## Interactive Rebase (Last 3 Commits)
```bash
git rebase -i HEAD~3
```

## Continue Rebase
```bash
git rebase --continue
```

## Abort Rebase
```bash
git rebase --abort
```

---

# 🌍 8. Remote Management

## View Remotes
```bash
git remote -v
```

## Add Remote
```bash
git remote add origin URL
```

## Change Remote URL
```bash
git remote set-url origin NEW_URL
```

## Remove Remote
```bash
git remote remove origin
```

---

# 🚀 9. Pushing

## Push Current Branch
```bash
git push
```

## First Push (Set Upstream)
```bash
git push -u origin branch-name
```

## Push All Branches
```bash
git push --all
```

## Push Tags
```bash
git push --tags
```

## Force Push (Dangerous)
```bash
git push --force
```

## Safer Force Push
```bash
git push --force-with-lease
```

---

# 📥 10. Pulling

## Pull
```bash
git pull
```

## Pull With Rebase (Recommended)
```bash
git pull --rebase
```

## Fetch Only
```bash
git fetch
```

---

# 🏷 11. Tags

## Create Lightweight Tag
```bash
git tag v1.0
```

## Create Annotated Tag
```bash
git tag -a v1.0 -m "Version 1.0"
```

## Push Tag
```bash
git push origin v1.0
```

---

# 🧳 12. Stashing

## Stash Changes
```bash
git stash
```

## List Stashes
```bash
git stash list
```

## Apply Stash
```bash
git stash apply
```

## Pop Stash
```bash
git stash pop
```

## Drop Stash
```bash
git stash drop
```

---

# 🔍 13. Viewing History

## Compact Log
```bash
git log --oneline --graph --all
```

## Detailed Log
```bash
git log
```

## Show Specific Commit
```bash
git show commit-hash
```

---

# ⏪ 14. Undoing Changes

## Reset Soft (Keep Changes)
```bash
git reset --soft HEAD~1
```

## Reset Mixed (Unstage Changes)
```bash
git reset HEAD~1
```

## Reset Hard (Dangerous)
```bash
git reset --hard HEAD~1
```

## Revert Commit (Safe for Shared Branches)
```bash
git revert commit-hash
```

---

# 🧱 15. Conflict Resolution Flow

1. Fix conflicts manually  
2. Add resolved files:
```bash
git add .
```
3. Continue:

If merging:
```bash
git merge --continue
```

If rebasing:
```bash
git rebase --continue
```

---

# 🏗 16. Feature Branch Workflow

```bash
git checkout main
git pull --rebase

git checkout -b feature-x
# do work
git add .
git commit -m "feature complete"

git checkout main
git pull --rebase
git merge feature-x

git push
```

---

# 🏢 17. Clean Professional Workflow

```bash
git pull --rebase
git add .
git commit -m "clear descriptive message"
git push
```

---

# 🔥 18. Git Best Practices

- Pull before push
- Use feature branches
- Use `--force-with-lease` instead of `--force`
- Never commit secrets
- Keep commits small and atomic
- Write meaningful commit messages

---

# ⚠️ 19. Emergency Commands

## See What Changed
```bash
git diff
```

## Find Who Changed a Line
```bash
git blame filename
```

## Clean Untracked Files
```bash
git clean -fd
```

---

# 🧭 20. SSH Setup (Linux Recommended)

Generate SSH Key:
```bash
ssh-keygen -t ed25519 -C "email@example.com"
```

Change Remote to SSH:
```bash
git remote set-url origin git@github.com:user/repo.git
```

---

# 🧠 Mental Model

Working Directory → Staging Area → Commit → Remote

```
edit → add → commit → push
```

---

# 🏆 Useful Aliases

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.cm commit
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all"
```

---

# 🎯 Core Commands to Master

- add
- commit
- branch
- merge
- rebase
- push
- pull
- reset
- stash

Master these and you control Git.