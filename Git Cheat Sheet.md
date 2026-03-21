# 🌿 Git Cheat Sheet

**Git** is a distributed version control system. This sheet covers everyday **workflow**, **history**, **staging**, **branches**, **remotes**, and safe **recovery** patterns.

> [!TIP]
> Modern Git (2.23+) prefers **`git switch`** (change branch) and **`git restore`** (discard/unstage files) over some **`git checkout`** forms—both styles are shown below.

---

## 🔄 Workflow — Start a Repository

| Command | Description |
| :--- | :--- |
| `git init` | Create a new repository in the current directory |
| `git clone <url>` | Clone a remote repository into a new folder (named from URL) |
| `git clone <url> <dir>` | Clone into a specific directory name |
| `git clone --depth 1 <url>` | **Shallow** clone (single revision; faster, CI-friendly) |

---

## 📜 History

| Command | Description |
| :--- | :--- |
| `git log` | Show commit history |
| `git log --oneline` | One line per commit (short hash + subject) |
| `git log -n 10` | Limit to last **10** commits |
| `git log --graph --oneline --decorate --all` | ASCII graph of branches |
| `git show` | Show the **latest** commit (patch + metadata) |
| `git show <commit>` | Show a specific commit |
| `git shortlog -sn` | Summarize commits **by author** (count + name) |
| `git blame <file>` | Line-by-line: last commit that touched each line |
| `git log -p <file>` | History **with patches** for a file |

---

## ✏️ Local Changes — Status, Stage, Diff, Commit

| Command | Description |
| :--- | :--- |
| `git status` | Working tree + staging area status |
| `git status -sb` | **Short** branch + tracking summary |
| `git add <file>` | Stage a file for the next commit |
| `git add -A` | Stage **all** changes (tracked + new, respects ignores) |
| `git add -p` | Stage **interactively** (hunks)—great for partial commits |
| `git diff` | Diff **unstaged** changes (working tree vs index) |
| `git diff --staged` | Diff **staged** changes (index vs last commit) |
| `git diff <commit1> <commit2>` | Diff two commits |
| `git commit` | Open editor for message; create commit from index |
| `git commit -m "message"` | Commit with message on the command line |
| `git commit -am "message"` | Stage **tracked** files and commit (no new files) |
| `git commit --amend` | **Replace** last commit (message and/or contents)—rewrites history |
| `git commit --amend --no-edit` | Amend keeping the same commit message |

### Unstage without losing edits

| Command | Description |
| :--- | :--- |
| `git restore --staged <file>` | Unstage file (Git 2.23+) |
| `git reset HEAD <file>` | Legacy equivalent of unstage |

### Discard local changes to a file

| Command | Description |
| :--- | :--- |
| `git restore <file>` | Discard changes in working tree (Git 2.23+) |
| `git checkout -- <file>` | Legacy equivalent (**destructive** to uncommitted work) |

> [!WARNING]
> **`git restore <file>`** / **`git checkout -- <file>`** permanently discard uncommitted edits to that file. Stash first if unsure (`git stash push`).

---

## 🌳 Branches

| Command | Description |
| :--- | :--- |
| `git branch` | List **local** branches (`*` = current) |
| `git branch -r` | List **remote-tracking** branches |
| `git branch -a` | List **local + remote-tracking** |
| `git branch <name>` | Create branch **without** switching |
| `git checkout <branch>` | Switch to an existing branch |
| `git checkout -b <branch>` | Create and switch (legacy combo) |
| `git switch <branch>` | Switch branch (modern) |
| `git switch -c <branch>` | Create and switch (modern) |
| `git merge <branch>` | Merge **`<branch>`** into the **current** branch |
| `git merge --no-ff <branch>` | Always create a merge commit |
| `git branch -d <branch>` | Delete merged local branch (**safe**) |
| `git branch -D <branch>` | Force delete local branch |
| `git branch -m <old> <new>` | Rename a branch |

---

## 🌍 Remotes — Fetch, Pull, Push

| Command | Description |
| :--- | :--- |
| `git remote -v` | List remotes with URLs |
| `git remote add origin <url>` | Add remote named `origin` |
| `git fetch` | Download objects from **default** remote (no merge) |
| `git fetch origin` | Fetch from `origin` |
| `git pull` | `fetch` + merge/rebase into current branch (default remote/branch) |
| `git pull --rebase` | Pull with **rebase** instead of merge |
| `git push` | Push current branch to configured upstream |
| `git push -u origin <branch>` | Push branch and set **upstream** tracking |
| `git push origin --delete <branch>` | Delete remote branch |

---

## 📦 Stash — Save Work in Progress

| Command | Description |
| :--- | :--- |
| `git stash` | Stash tracked changes (default message) |
| `git stash push -m "msg"` | Stash with a message |
| `git stash push -u` | Include **untracked** files |
| `git stash list` | List stashes |
| `git stash pop` | Apply latest stash and remove it from list |
| `git stash apply` | Apply latest stash **keep** stash entry |
| `git stash drop stash@{n}` | Delete a specific stash |

---

## 🏷️ Tags

| Command | Description |
| :--- | :--- |
| `git tag` | List tags |
| `git tag v1.0.0` | Create **lightweight** tag at HEAD |
| `git tag -a v1.0.0 -m "Release"` | Create **annotated** tag |
| `git push origin v1.0.0` | Push a tag |
| `git push origin --tags` | Push **all** tags |

---

## ↩️ Undo & Recovery (Careful)

| Command | Description |
| :--- | :--- |
| `git reset --soft HEAD~1` | Move branch back 1 commit; **keep** changes staged |
| `git reset --mixed HEAD~1` | Move back 1 commit; changes **unstaged** (default) |
| `git reset --hard HEAD~1` | Move back 1 commit; **discard** working tree/index (**danger**) |
| `git revert <commit>` | Create a **new** commit that undoes `<commit>` (safe for shared history) |
| `git reflog` | History of HEAD movements—**recovery** after mistakes |

> [!WARNING]
> **`git reset --hard`** and **rewriting pushed history** (`push --force`) can disrupt teammates. Prefer **`git revert`** on **shared** branches.

---

## ⚔️ Merge Conflicts — Minimal Flow

1. Run `git merge <branch>` or `git pull` → conflict markers appear in files.
2. Edit files to resolve (`<<<<<<<`, `=======`, `>>>>>>>`).
3. `git add <resolved-files>`
4. `git commit` (merge commit) or continue rebase with `git rebase --continue`.

| Command | Description |
| :--- | :--- |
| `git merge --abort` | Abort in-progress merge |
| `git rebase --abort` | Abort in-progress rebase |

---

## 🔁 Rebase (Basics)

| Command | Description |
| :--- | :--- |
| `git rebase main` | Replay current branch commits **on top of** `main` |
| `git rebase -i HEAD~3` | **Interactive** rebase last 3 commits (squash, reword, drop) |

> [!NOTE]
> **Do not rebase** commits that already exist on **shared** branches unless your team agrees (use coordinated force-with-lease pushes).

---

## 🙈 `.gitignore` — Quick Patterns

| Pattern | Meaning |
| :--- | :--- |
| `node_modules/` | Ignore directory |
| `*.log` | Ignore all `.log` files |
| `!.env.example` | **Negate** ignore (keep this file) |
| `build/` | Ignore build output folder |

---

## ⚙️ Useful Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global pull.rebase false   # or true / merges only, team policy
git config --global core.editor "vim"
```

List settings: `git config --list --show-origin`

---

## 📌 Quick Reference

| Goal | Command |
| :--- | :--- |
| New repo | `git init` |
| Clone | `git clone <url>` |
| Stage all | `git add -A` |
| Commit | `git commit -m "msg"` |
| New branch & switch | `git switch -c feature` |
| Update from remote | `git pull` |
| Publish branch | `git push -u origin feature` |
| Save WIP | `git stash push -u -m "wip"` |
| Who changed line | `git blame <file>` |

---

## 🔗 Official Docs

| Resource | URL |
| :--- | :--- |
| Git Book | [https://git-scm.com/book](https://git-scm.com/book) |
| Reference | [https://git-scm.com/docs](https://git-scm.com/docs) |

---

*Commit often, write clear messages, and avoid **`--force`** on shared branches unless the team expects it.*
