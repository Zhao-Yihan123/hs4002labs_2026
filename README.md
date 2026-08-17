## HS4002/HS4902 — Submitting Lab Notebooks with Git (Student Memo)

Starting week 2, you'll submit your weekly lab notebook through GitHub
using the **fork-and-pull-request** workflow — the same one used by
essentially every open-source project and most software teams. It looks
like a lot of steps the first time. By week 4 it's muscle memory. Read
this once fully before you touch a terminal.

You'll need a free GitHub account (sign up at github.com if you don't
have one) and `git` installed on your machine (already covered in week 2
lab; run `git --version` in your terminal to check). You'll also want the
**GitHub CLI** (`gh`) installed — it makes authentication painless (see
Step 0 below). Check with `gh --version`; if it's missing:
- **Mac:** `brew install gh`
- **Windows:** `winget install --id GitHub.cli` (or the installer at
  cli.github.com)

**Windows students:** run every command in this memo inside **Git Bash**
(installed automatically alongside Git for Windows), not PowerShell or
Command Prompt. Everything here is bash syntax (`mkdir -p`, forward-slash
paths, etc.) — PowerShell doesn't understand some of these the same way
and will just confuse you.

### The mental model

- **Upstream** (`xiangyum/hs4002labs_2026`) is my copy.
  It's a **public** repo, so anyone can view it, but you cannot push to it
  directly.
- **Your fork** is your own copy of that repo, living under your GitHub
  account. You have full write access to it. Because the upstream repo is
  public, your fork is public too — GitHub doesn't allow private forks of
  a public repo.
- A **pull request (PR)** is how you turn in the week's work — it's a
  link to your branch's diff against `main` that I check off in my
  grading spreadsheet. Opening a PR does **not** hand over your code or
  merge it into anything. I will not be merging your PRs into the
  upstream repo, and I won't be leaving comments on them either — grading
  happens off GitHub, in the spreadsheet, not through PR review. If
  something's wrong with a submission, I'll follow up with you directly
  (office hours, course channel) rather than on the PR itself.
- **Note on visibility:** because the repo is public, your fork and any
  PR you open are visible to anyone on the internet — not just me and
  your classmates. Keep that in mind for what you put in your submission.

### 0. One-time setup (do this once, before week 2's lab)

1. **Fork the repo.** Go to the `hs4002labs_2026` repo on GitHub
   (link posted separately) and click **Fork** (top right). This creates
   `github.com/<your-username>/hs4002labs_2026`.
2. **Clone your fork** to your machine — not the original:
   ```bash
   git clone https://github.com/<your-username>/hs4002labs_2026.git
   cd hs4002labs_2026
   ```
3. **Add the original repo as `upstream`**, so you can pull in new lab
   templates as I release them:
   ```bash
   git remote add upstream https://github.com/xiangyum/hs4002labs_2026.git
   git remote -v
   ```
   You should see `origin` pointing at your fork and `upstream` pointing
   at mine. `origin` is where you push; `upstream` is where you pull new
   material from. Fetching from `upstream` never needs a login — it's a
   public repo, so anyone can read it.
4. **Authenticate git so you can push to your fork.** Run:
   ```bash
   gh auth login
   ```
   and pick **GitHub.com → HTTPS → Login with a web browser**, then
   follow the one-time code it gives you. This is a one-time setup per
   machine — after this, `git push` just works with no further prompts.
   (No `gh`? Git will instead prompt for a username and password the
   first time you push — for the password, paste a **Personal Access
   Token** from GitHub Settings → Developer settings → Personal access
   tokens, not your account password; GitHub stopped accepting account
   passwords for git operations in 2021.)

### 1. The weekly cycle

Repeat this every week, replacing `weekN` with your actual week number.
Each week's folder (`labs/weekN/`) already exists in the repo — I seed it
with that week's notebook template before the lab, so there's no folder
for you to create.

**Step 1 — sync with upstream, so you have that week's template:**
```bash
git checkout main
git fetch upstream
git merge upstream/main
git push origin main
```

**Step 2 — create a fresh branch for the week's work.** Don't work
directly on `main` — branching keeps each week's submission isolated and
makes the PR diff clean:
```bash
git checkout -b week1
```
Switching branches (`checkout`) can make files appear or disappear in
Finder/Explorer if they only exist on one branch — that's expected, not
a bug, and nothing is lost. Nothing gets deleted; it's just not the
branch you're currently looking at. Switch back to where you were and
everything reappears exactly as you left it.

**Step 3 — do the lab.** Edit the notebook inside
`labs/week1/`. Commit as you go, not just once at the end —
frequent, small commits with meaningful messages are part of what's being
assessed here (and they're a lifesaver if something breaks and you need
to see what changed):
```bash
git add labs/week1/
git commit -m "Complete week 1 lab exercises"
```

**Step 4 — push your branch to your fork** (not upstream — you can't
push there):
```bash
git push origin week1
```

**Step 5 — open the PR.** On GitHub, go to your fork; you'll see a
banner offering to "Compare & pull request" for the branch you just
pushed. Click it. Confirm:
- **base repository:** `xiangyum/hs4002labs_2026`, base `main`
- **head repository:** your fork, compare `week1`

Fill in the PR template (name, matric number, week) and submit. That's
your submission — the PR link is what counts as turned in, not a file
you email me.

### 2. A cheat sheet

| You want to... | Command |
|---|---|
| See what's changed / staged | `git status` |
| Stage a file | `git add <path>` |
| Commit staged changes | `git commit -m "message"` |
| See commit history | `git log --oneline` |
| Switch branches | `git checkout <branch>` |
| Create + switch to a new branch | `git checkout -b <branch>` |
| Pull new material from me | `git fetch upstream` then `git merge upstream/main` |
| Push your branch to your fork | `git push origin <branch>` |
| Undo uncommitted changes to a file | `git checkout -- <path>` |
| See the diff before committing | `git diff` |

### 3. Ground rules

- Work only inside that week's `labs/weekN/` folder. Don't edit other
  weeks' folders — this matters once you're syncing from `upstream`.
- One PR per week, opened from a branch named `weekN`.
- Commit early and often. A PR with a single giant commit the night it's
  due tells me nothing about your process; a PR with a real trail of
  commits does.
- If you're stuck on git itself (not the stats), ask — that's what week
  2's lab and office hours are for. Losing an evening to a merge conflict
  instead of the actual assignment helps no one.


### 4. Common problems

**"Please tell me who you are" when committing.** Git doesn't know your
name/email yet — happens on a completely fresh install. Fix once:
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

**"Permission denied (publickey)" when pushing/fetching.** This means
your remote is pointed at an SSH URL (`git@github.com:...`) instead of
HTTPS, and this machine has no SSH key registered with GitHub. Check with
`git remote -v` — if you see `git@github.com`, switch it:
```bash
git remote set-url origin https://github.com/<your-username>/hs4002labs_2026.git
git remote set-url upstream https://github.com/xiangyum/hs4002labs_2026.git
```
Then re-run Step 4 of the one-time setup (`gh auth login`) if you haven't
already, and try again.

**Git prompts for a username and password when pushing, and your GitHub
password doesn't work.** Expected — GitHub stopped accepting account
passwords for git operations in 2021. Run `gh auth login` once (see Step
4 of the one-time setup) and this goes away entirely; or, for the
password prompt specifically, paste a Personal Access Token (GitHub
Settings → Developer settings → Personal access tokens) instead of your
actual password.

**Push fails even though `gh auth login` worked.** If you've used `gh` for
something else before (a personal project, another course), it may be
logged into a different GitHub account than the one you forked with.
Check which account is active:
```bash
gh auth status
gh auth switch
```

**Push fails with "remote: Permission to xiangyum/hs4002labs_2026.git
denied to `<your-username>`."** You cloned or pushed straight to the
upstream repo instead of your fork — easy to do if you copied the URL
from the address bar instead of clicking **Fork** first. Check
`git remote -v`: `origin` should point at
`github.com/<your-username>/hs4002labs_2026`, not
`github.com/xiangyum/hs4002labs_2026`. If it's wrong, go fork the repo
properly, then fix the remote:
```bash
git remote set-url origin https://github.com/<your-username>/hs4002labs_2026.git
```

**"failed to push some refs" / "non-fast-forward."** Your local branch
is behind — usually because you forgot Step 1. Run `git pull origin
<branch>` first, resolve any conflicts, then push again.

**"fatal: Need to specify how to reconcile divergent branches."** You'll
see this if `git pull` finds that your local branch and the remote one
have both moved forward independently — recent versions of git refuse to
guess whether you want a merge or a rebase, and ask you to set a default
once:
```bash
git config --global pull.rebase false
git pull origin main
```
This sets plain merging as your default (matching everything else in
this workflow — you'll never need to rebase here), and only needs to be
run once per machine.

**Merge conflicts when syncing `upstream/main`.** This happens if you
edited a file I've since updated in the template (rare, but possible).
Git will mark the
conflicting sections in the file with `<<<<<<<`, `=======`, `>>>>>>>`.
Open the file, decide what should stay, delete the markers, then:
```bash
git add <file>
git commit
```
If the conflict is inside an `.ipynb` file, this is noticeably harder
than resolving a conflict in plain code — you're editing raw notebook
JSON, not clean cell text, and the conflict markers can land mid-cell in
a way that's easy to mangle. Go slowly, and if it looks unrecoverable,
office hours beats guessing.

**Your PR shows a huge, unrelated-looking diff.** Usually means you
branched off a stale `main` — you skipped Step 1's sync before creating
your branch, so your branch is missing commits `upstream/main` already
has, and the PR diff includes those on top of your actual work. Fix by
merging `upstream/main` into your branch (or, cleaner, delete the branch,
sync `main` properly per Step 1, and re-branch).

**Detached HEAD state.** If `git status` says `HEAD detached at
<hash>`, you checked out a specific commit instead of a branch — easy to
do with a typo, or if you clicked a commit link on GitHub and copied a
command from there. You can look around safely, but don't commit here;
anything you commit in this state can get lost the moment you switch
branches. Get back to solid ground with `git checkout main` (or
`git checkout -b rescue-branch` first if you'd made changes you want to
keep).

**You committed to `main` by accident instead of a branch.** Don't
panic, don't force-push. Come to office hours or post in the course
channel — this is fixable, but the fix depends on exactly what happened.

**A push is slow, or GitHub rejects it for a large file.** Usually means
a dataset or output file got swept into `git add` by accident — check
`git status` before adding, and keep raw data out of your submission
folder (reference it or regenerate it in the notebook instead of
committing it). GitHub hard-blocks any single file over 100MB.
