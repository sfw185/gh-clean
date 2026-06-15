# gh-clean

A [`gh`](https://cli.github.com) CLI extension that deletes local git branches
that are clearly done, including review branches you pulled down and forgot,
and stale local experiments you never opened a PR for.

## Install

```sh
gh extension install sfw185/gh-clean
```

## Usage

```sh
gh clean              # base = main
gh clean --base dev   # any other base branch
gh clean --dry-run    # preview without deleting
gh clean --help       # full help
```

## What gets deleted

A local branch is deleted if any of these are true:

| Rule | Description |
|---|---|
| **Merged** | Branch is reachable from the base, *or* its squashed equivalent is already in base (detected via `git commit-tree` + `git cherry`). |
| **PR closed** | A GitHub PR exists for the branch and is `MERGED` or `CLOSED`. Requires `gh` to be authenticated. |
| **Foreign author** | The tip commit was authored by someone other than you (`git config user.email`). Catches review branches you pulled down to look at and never cleaned up. |
| **Stale orphan** | Yours, no PR ever opened, and the tip hasn't moved in 7 days (override with `GH_CLEAN_STALE_DAYS`). Guarded: never fires if `gh` data isn't available, so missing PR info can't be misread as "no branch has a PR". |

Branches checked out in any worktree (`git worktree list`) are never deleted.

## Why not `gh-poi` or `git-trim`?

[`gh-poi`](https://github.com/seachicken/gh-poi) and
[`git-trim`](https://github.com/foriequal0/git-trim) cover the first two cases
well. `gh-clean` adds the **foreign-author** and **stale-orphan** rules, useful
if you pull down others' branches for review, or if you experiment locally
without always opening a PR.

## Environment

| Variable | Default | Purpose |
|---|---|---|
| `GH_CLEAN_STALE_DAYS` | `7` | Days a branch can sit untouched before the stale-orphan rule fires. |

## License

MIT
