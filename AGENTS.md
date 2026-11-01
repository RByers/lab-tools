# AGENTS.md

## Repository

The canonical remote is https://github.com/RByers/lab-tools (`origin`). It is
served by GitHub Pages at https://lt.rbyers.ca/ straight from `main`, with no
build step, so whatever lands on `main` is live.

## Git workflow
- main is my primary branch and origin and local should be kept in-sync
- Before starting any task, ALWAYS REMEMBER to fetch and rebase main onto
  origin/main, DO NOT work from a stale local main!
- Do all work in a branch on a work tree, branched from main
- Whenever a set of changes is complete, commit and push that to origin/main (no pull requests).
- Then, before cleaning up the worktree, fast-forward main by rebasing from origin/main.
- Only then can you clean up and delete the worktree and branch you created.

Always remember that multiple agents are operating again origin/main at once
at the user will expect you to have all the latest context before planing a
task and when finalizing the task. 

### Pushing to origin/main is expected — don't stop to ask

This repo has no PR review step, so pushing straight to origin/main *is* the
normal way work lands here. Treat this section as standing authorization.

Some agents carry a general "never push to main" rule. That rule does not
apply to this repo: it exists to protect a reviewed shared branch, and there
isn't one here. Push when the work is complete and verified, rather than
leaving it on a branch and asking. If your harness makes that genuinely
impossible, push the branch and say so explicitly — don't silently leave the
work unlanded.

Do still stop and ask before anything that could destroy work: force-pushing,
rewriting already-pushed history, deleting branches you didn't create, or
pushing when the push isn't a fast-forward.

### The credentials are restricted — GitHub is the backstop

The agent pushes with restricted credentials, and GitHub permissions block
the genuinely unsafe operations rather than relying on the agent to hold
back. Rewriting published history is not allowed: force-pushes and
non-fast-forward pushes to main are rejected by the server, so an ordinary
push can add commits but can never drop or rewrite one that already landed.

Two things follow. First, a normal `git push origin HEAD:main` is a low-risk
operation here — the worst realistic outcome is a commit you'd rather not
have, fixable with a follow-up commit. Don't treat it as irreversible.
Second, don't try to route around a rejected push. If the server refuses one,
that's the guardrail doing its job: rebase onto origin/main and push again,
and if it still refuses, stop and report rather than reaching for --force.

Anything genuinely destructive that the permissions *don't* cover — deleting
branches, discarding a worktree with unpushed commits — still needs asking
first, since no server-side rule will catch those.

### Don't leave stray branches on origin

Work lands on main, so a branch on origin is just noise once it's merged.
Push your worktree branch only if you actually need it shared. After landing
on main, delete any remote branch you created:

```
git push origin --delete <branch>
```

Before deleting, confirm nothing is lost: `git merge-base --is-ancestor
<branch> origin/main`. If that fails the branch still has unique commits —
check whether main already supersedes their *content* (a parallel agent
redoing the same task is the common case) and say what you found rather than
deleting on the ancestry check alone.
