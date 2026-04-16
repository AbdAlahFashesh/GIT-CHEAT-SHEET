# Git Katas Cheat Sheet (Personal Reference)

---

##  1. Basic Add / Commit / Status

### Commands
```bash
git status
git add <file>
git commit -m "message"
git log --oneline --graph
```

### When to use
Save your work after making changes.

### Key idea
- Working directory → your edits  
- Staging area → what will be committed  
- Repository → saved history  

---

##  2. Staging Area

### Commands
```bash
git diff
git diff --staged
git add file.txt
git restore --staged file.txt
git restore file.txt
```

### Key idea
A file can exist in:
- working directory (unstaged)
- staging area (staged)

---

##  3. Branching Basics

### Commands
```bash
git branch
git branch mybranch
git switch mybranch
git switch master
```

### Key idea
Branches are separate timelines of work.

---

##  4. Branch Comparison

### Commands
```bash
git log --oneline --graph --all
git diff mybranch master
```

---

##  5. Cherry Pick

### Commands
```bash
git cherry-pick <commit>
git cherry-pick <A>^..<B>
```

### Key idea
Copies commits from another branch.

---

##  6. Revert (Safe Undo)

### Commands
```bash
git revert <commit>
git show <commit>
```

### Key idea
Creates a new commit that undoes changes.

---

##  7. Reset (Dangerous)

### Commands
```bash
git reset --hard HEAD^
git reset --hard HEAD
```

Deletes changes permanently.

---

##  8. Stash

### Commands
```bash
git stash
git stash list
git stash apply
git stash apply --index
git stash drop
```

### Key idea
Temporarily saves work.

---

##  9. Clean

### Commands
```bash
git clean -n
git clean -n -d
git clean -f -d
```

### Key idea
Deletes untracked files.

---

##  10. Logs

### Commands
```bash
git log
git log --oneline
git log --oneline --graph --all
```

---

##  11. Windows Tips

- Use Git Bash for `.sh` files  
- Use PowerShell for `.ps1` files  

Fix script blocking:
```powershell
Set-ExecutionPolicy RemoteSigned
```

---

##  Emergency Commands

```bash
git status
git log --oneline --graph
git diff
git add .
git commit -m "quick save"
git stash
git reset --hard HEAD
```
