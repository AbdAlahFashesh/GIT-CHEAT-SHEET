# Git Katas Quick Reference

Personal cheat sheet from the beginner Git katas.

## Everyday loop
- `git status` — where I am, what is staged, unstaged, untracked
- `git log --oneline --graph -n 5` — quick history
- `git add file.txt` — stage current version
- `git commit -m "message"` — save staged snapshot
- `git diff` — unstaged changes
- `git diff --staged` — staged changes

## The 3 places Git cares about
- **Working directory** = what is currently in my files
- **Staging area** = what will go into the next commit
- **Repository** = committed history
- A file can be different in the working directory and staging area at the same time

Memory trick: **edit -> add -> commit**. If I edit again after `git add`, the new edit is not included until I add again.

## Branching and switching
- `git branch` — see branches
- `git branch mybranch` — create branch
- `git switch mybranch` — switch branch
- `git log --oneline --graph --all` — see all branch history
- Switching branches can make files appear/disappear because each branch has its own snapshot

## Compare and move changes across branches
- `git diff mybranch master` — compare branches
- `git cherry-pick HASH` — copy one commit onto current branch
- `git reset --hard HEAD^` — remove last local cherry-pick / commit
- `git cherry-pick F^..G` — cherry-pick a range including F and G
- Do **not** type `< >` around hashes

## Safe undo vs destructive undo
- `git revert HASH` — safe undo for public history; adds a new commit
- `git reset --hard HEAD` — throw away local staged + unstaged work
- `git show HASH` — inspect an old commit

## Stash
- `git stash` — save staged and unstaged work temporarily
- `git stash list` — see saved stashes
- `git stash apply` — bring stash back, usually all unstaged
- `git stash apply --index` — bring stash back and restore staged state too
- `git stash drop` — delete stash when done

## Cleaning generated / untracked files
- `git clean -n` — preview what would be deleted
- `git clean -n -d` — preview files + directories
- `git clean -f -d` — actually delete untracked files + directories
- Stage an important untracked file first if I want to protect it

## Restore / unstage
- `git restore --staged file.txt` — unstage file
- `git restore file.txt` — discard working copy changes

## PowerShell / Windows notes
- `.sh` files are for Git Bash, not PowerShell
- In PowerShell use `./setup.ps1`
- If scripts are blocked: `Set-ExecutionPolicy RemoteSigned`
- `source setup.sh` works in **Git Bash**

## Mistakes I hit
- Typed placeholders literally like `git cherry-pick <hash>` — wrong
- Mistyped commit hashes — check the hash first
- Forgot that one file can be both staged and unstaged at once
- Forgot that `git stash apply` may unstage everything unless I use `--index`

## When I get stuck
1. `git status`
2. `git diff`
3. `git diff --staged`
4. `git log --oneline --graph -n 8`
5. If I need to pause work: `git stash`
6. If I need safe undo: `git revert HASH`
7. Be extra careful with `reset --hard` and `clean -f -d`

## Best habit
Run `git status` constantly.
