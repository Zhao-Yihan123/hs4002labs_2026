## HS4002/HS4902 — Submitting Lab Notebooks with Git (Student Memo)

Starting week 2, you'll submit your weekly lab notebook through GitHub
using the **fork-and-pull-request** workflow — the same one used by
essentially every open-source project and most software teams. It looks
like a lot of steps the first time. By week 4 it's muscle memory. Read
this once fully before you touch a terminal.

You'll need a free GitHub account (sign up at github.com if you don't
have one) and `git` installed on your machine (already covered in week 2
lab; run `git --version` in your terminal to check).

### The mental model

- **Upstream** (`xiangyum/hs4002labs_2026`) is my copy.
  It's a **public** repo, so anyone can view it, but you cannot push to it
  directly.
- **Your fork** is your own copy of that repo, living under your GitHub
  account. You have full write access to it. Because the upstream repo is
  public, your fork is public too — GitHub doesn't allow private forks of
  a public repo.
- A **pull request (PR)** is you asking me to look at a set of changes on
  your fork and review them. Opening a PR does **not** hand over your
  code or merge it into anything — it just puts it in front of me for
  comments. I will not be merging your PRs into the upstream repo; the PR
  itself, plus my comments on it, is what gets graded.
- **Note on visibility:** because the repo is public, your fork and any
  PR you open are visible to anyone on the internet — not just me and
  your classmates. Keep that in mind for what you put in your submission.

### 0. One-time setup (do this once, before week 2's lab)

1. **Fork the repo.** Go to the `hs4002labs_2026` repo on GitHub
   (link posted separately) and click **Fork** (top right). This creates
   `github.com/<your-username>/hs4002labs_2026`.
2. **Clone your fork** to your machine — not the original:
   ```bash
   git clone git@github.com:<your-username>/hs4002labs_2026.git
   cd hs4002labs_2026
   ```
3. **Add the original repo as `upstream`**, so you can pull in new lab
   templates as I release them:
   ```bash
   git remote add upstream git@github.com:xiangyum/hs4002labs_2026.git
   git remote -v
   ```
   You should see `origin` pointing at your fork and `upstream` pointing
   at mine. `origin` is where you push; `upstream` is where you pull new
   material from.
4. **Make your submission folder**, using your matric number as the
   folder name (I'll use `e0123456` as a placeholder below — substitute
   your own):
   ```bash
   mkdir -p labs/week2/e0123456
   ```

### 1. The weekly cycle

Repeat this every week, replacing `weekN` and `e0123456` with your actual
week and matric number.

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
git checkout -b week2-e0123456
```

**Step 3 — do the lab.** Edit the notebook inside
`labs/week2/e0123456/`. Commit as you go, not just once at the end —
frequent, small commits with meaningful messages are part of what's being
assessed here (and they're a lifesaver if something breaks and you need
to see what changed):
```bash
git add labs/week2/e0123456/
git commit -m "Complete week 2: bivariate analysis exercises"
```

**Step 4 — push your branch to your fork** (not upstream — you can't
push there):
```bash
git push origin week2-e0123456
```

**Step 5 — open the PR.** On GitHub, go to your fork; you'll see a
banner offering to "Compare & pull request" for the branch you just
pushed. Click it. Confirm:
- **base repository:** `xiangyum/hs4002labs_2026`, base `main`
- **head repository:** your fork, compare `week2-e0123456`

Fill in the PR template (name, matric number, week) and submit. That's
your submission — the PR link is what counts as turned in, not a file
you email me.

**Step 6 — respond to feedback.** I'll leave comments directly on the
PR. If I request changes, just keep working on the *same branch* and push
again:
```bash
git add labs/week2/e0123456/
git commit -m "Address feedback: fix logistic regression interpretation"
git push origin week2-e0123456
```
The PR updates automatically — you don't need to open a new one.

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

### 3. Common problems

**"Permission denied (publickey)" when pushing.** You haven't set up SSH
keys with GitHub, or you cloned with the wrong URL. Either add an SSH key
(GitHub's docs: *Settings → SSH and GPG keys*), or clone/push using the
HTTPS URL instead and authenticate with a personal access token when
prompted.

**"failed to push some refs" / "non-fast-forward."** Your local branch
is behind — usually because you forgot Step 1. Run `git pull origin
<branch>` first, resolve any conflicts, then push again.

**Merge conflicts when syncing `upstream/main`.** This happens if you
edited a file I've since updated in the template (rare, but possible if
you experiment outside your own `labs/weekN/e0123456/` folder — another
reason to keep your work scoped to your own folder). Git will mark the
conflicting sections in the file with `<<<<<<<`, `=======`, `>>>>>>>`.
Open the file, decide what should stay, delete the markers, then:
```bash
git add <file>
git commit
```

**You committed to `main` by accident instead of a branch.** Don't
panic, don't force-push. Come to office hours or post in the course
channel — this is fixable, but the fix depends on exactly what happened.

**Notebook diffs are unreadable in the PR** (huge JSON blobs instead of
clean cell-by-cell changes). This is normal for `.ipynb` files — GitHub
renders a reasonable diff for Jupyter notebooks natively, but if it looks
garbled, clear your cell outputs before committing
(`Kernel → Restart & Clear Output` in Jupyter, or `Cell → All Output →
Clear` depending on your interface) and re-run only what you need to
confirm it works.

### 4. Ground rules

- Work only inside your own `labs/weekN/<matric_no>/` folder. Don't edit
  other students' folders or files outside your own directory — you
  won't be able to see their forks anyway, but this matters once you're
  syncing from `upstream`.
- One PR per week, opened from a branch named `weekN-<matric_no>`.
- Commit early and often. A PR with a single giant commit the night it's
  due tells me nothing about your process; a PR with a real trail of
  commits does.
- If you're stuck on git itself (not the stats), ask — that's what week
  2's lab and office hours are for. Losing an evening to a merge conflict
  instead of the actual assignment helps no one.
