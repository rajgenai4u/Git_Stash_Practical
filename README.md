# Git Stash Practical — Git_Stash_Practical

Hands-on practical demonstrating core Git workflows: repository initialization, branching,
committing, hotfixing, and **git stash** (saving uncommitted work temporarily while switching branches).

- **Repository:** https://github.com/rajgenai4u/Git_Stash_Practical
- **Author:** Karre Rajesh (`rajsai4us@gmail.com`)
- **Date:** 23 September 2026
- **Project file:** `app.py` (small Python application used as the working example)

---

## 1. Project Description

A minimal Python application used purely to practice Git operations:

```python
def main():
    print("Base Application Running - Critical Bug Fixed!")
if __name__ == "__main__":
    main()
```

The practical walks through:

1. Initializing a Git repository and making the first commit on `main`.
2. Creating a feature branch (`feature-payments`) for work-in-progress feature code.
3. Switching back to `main` to deliver an urgent hotfix while feature work was unfinished.
4. Using **`git stash`** to shelve uncommitted feature changes, apply the hotfix, and then
   restore the stashed work on the feature branch.
5. Committing the feature work and pushing branches to GitHub.

---

## 2. Branches

| Branch | Status | Last Commit | Description |
|---|---|---|---|
| `main` | ✅ Pushed to `origin/main` | `199bf30` — *Hotfix: Fix critical bug on main* | Stable base application + critical bug fix |
| `feature-payments` | Local (created from `main`) | `567f4ad` — *final save on feature-payments* | In-progress Stripe payment processing feature |

### Branch topology

```
* 567f4ad (feature-payments) final save on feature-payments
| * 199bf30 (main, origin/main) Hotfix: Fix critical bug on main
|/
* 3014d33 Intial commit to main
```

Both branches diverge from the initial commit `3014d33`.

---

## 3. Commit History

| # | Commit | Branch | Date | Message | Changes |
|---|---|---|---|---|---|
| 1 | `3014d33` | `main` | 2026-09-23 22:31 | `Intial commit to main` | Created `app.py` (+4 lines) |
| 2 | `199bf30` | `main` | 2026-09-23 22:37 | `Hotfix: Fix critical bug on main` | Changed base message to `"Base Application Running - Critical Bug Fixed!"` |
| 3 | `567f4ad` | `feature-payments` | 2026-09-23 22:44 | `final save on feature-payments` | Added `print("Processing Stripe Payment... (Work in progress)")` (+2 lines) |

### Full log command

```bash
git log --all --graph --oneline --decorate
```

---

## 4. Timeline of Git Commands (from reflog)

Every step reconstructed from `git reflog`:

| Time | Action | Command(s) |
|---|---|---|
| 22:31:30 | Created initial commit on `main` | `git init` · `git add app.py` · `git commit -m "Intial commit to main"` |
| 22:32:27 | Created/switched to feature branch | `git checkout -b feature-payments` |
| 22:34:41 | Reset working tree (prep for stash demo) | `git reset HEAD` / `git reset --hard HEAD` |
| 22:36:03 | Switched back to `main` | `git checkout main` |
| 22:37:00 | Applied urgent hotfix and committed | `git commit -m "Hotfix: Fix critical bug on main"` |
| 22:38:08 | Returned to feature branch | `git checkout feature-payments` |
| 22:41:17 | Restored/reset after stash apply | `git stash apply` / `git stash pop` · `git reset HEAD` |
| 22:44:25 | Committed finished feature work | `git add app.py` · `git commit -m "final save on feature-payments"` |
| 22:44:49 | Switched back to `main` | `git checkout main` |
| — | Pushed to GitHub | `git push -u origin main` |

---

## 5. Git Stash — Commands Used / Reference

`git stash` temporarily shelves uncommitted changes so you can switch branches cleanly —
ideal when you are mid-feature on `feature-payments` and must jump to `main` for a hotfix.

### Commands used in this practical

```bash
# Start: on feature-payments with uncommitted WIP changes in app.py
git status                      # observe untracked/modified files
git stash                       # shelve uncommitted changes (stash entry created)

# Now the working tree is clean → safe to switch branches
git checkout main               # move to main for the urgent fix

# ... edit app.py, apply the critical bug fix ...
git add app.py
git commit -m "Hotfix: Fix critical bug on main"

# Return to feature work and restore the shelved changes
git checkout feature-payments
git stash list                  # verify the stashed entry still exists
git stash apply                 # re-apply changes BUT keep them in the stash
# — or —
git stash pop                   # re-apply changes AND drop them from the stash

# Finish the feature
git add app.py
git commit -m "final save on feature-payments"
git stash drop                  # remove stash entry if it was kept with `apply`
```

### Full git stash command reference

```bash
git stash                          # stash tracked uncommitted changes
git stash push -m "message"        # stash with a descriptive message
git stash push -u                  # include untracked files
git stash push -a                  # include untracked AND ignored files
git stash list                     # list all stashes (stash@{0} is newest)
git stash show stash@{0}           # show diff summary of a stash
git stash show -p stash@{0}        # show full patch of a stash
git stash apply                    # apply latest stash, keep it in the list
git stash apply stash@{1}          # apply a specific stash
git stash pop                      # apply latest stash and remove it
git stash drop                     # delete the latest stash
git stash drop stash@{1}           # delete a specific stash
git stash clear                    # delete ALL stashes
git stash branch <branch>          # create a new branch from a stash's commit
```

### Why stash was needed here

```
feature-payments:  print("Processing Stripe Payment... (Work in progress)")   ← uncommitted WIP
main:              needs "Critical Bug Fixed!" urgently                       ← hotfix
```

Without stash, unfinished feature changes would either block the branch switch
(`git checkout` refuses when changes would be overwritten) or leak into the hotfix commit.
Stash cleanly separates the two lines of work.

---

## 6. Complete Git Command Log (whole practical)

```bash
# ---------- Setup ----------
git init
git config user.name "Karre Rajesh"
git config user.email "rajsai4us@gmail.com"

# ---------- Initial commit on main ----------
# (created app.py)
git add app.py
git commit -m "Intial commit to main"

# ---------- Create feature branch ----------
git checkout -b feature-payments
# (started WIP: print("Processing Stripe Payment... (Work in progress)"))

# ---------- Stash the WIP and switch to main ----------
git status
git stash push -m "WIP: stripe payment processing"
git checkout main

# ---------- Hotfix on main ----------
# (edited app.py: "Base Application Running - Critical Bug Fixed!")
git add app.py
git commit -m "Hotfix: Fix critical bug on main"

# ---------- Back to feature branch, restore stash ----------
git checkout feature-payments
git stash list
git stash pop

# ---------- Save the feature work ----------
git add app.py
git commit -m "final save on feature-payments"

# ---------- Push to GitHub ----------
git remote add origin git@github.com:rajgenai4u/Git_Stash_Practical.git
git push -u origin main
# git push -u origin feature-payments   # push feature branch when ready

# ---------- Verification ----------
git status
git log --all --graph --oneline --decorate
git branch -a
git stash list
```

---

## 7. How to Clone and Reproduce

```bash
git clone git@github.com:rajgenai4u/Git_Stash_Practical.git
cd Git_Stash_Practical

git log --all --graph --oneline --decorate   # view history
git checkout feature-payments                # inspect feature work
python app.py                                # run the app
```

### Reproduce the stash workflow yourself

```bash
git checkout main
git checkout -b demo-stash
# edit app.py (leave uncommitted)
git stash push -m "demo WIP"
git checkout main
git stash list
git stash pop
```

---

## 8. Key Learnings

- `git stash` saves uncommitted changes to a temporary stack and cleans the working tree.
- Stashes are **local** — they are never pushed to the remote repository.
- `git stash apply` keeps the stash; `git stash pop` applies and removes it.
- `git stash list` / `git stash show -p` let you inspect shelved work before restoring.
- Stashing enabled an urgent `main` hotfix while `feature-payments` work was still in progress,
  keeping the two changesets cleanly separated.
- Branching + stashing together provide a safe, non-destructive way to juggle parallel tasks.

---

*Practical completed by Karre Rajesh on 23 September 2026.*
