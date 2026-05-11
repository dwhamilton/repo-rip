# repo-ledger

A tiny CLI for retiring local Git repos without losing track of them.

`repo-ledger` creates a lightweight Markdown inventory file for a Git repository, then leaves that file beside the repo directory.

Instead of keeping every old repo cloned locally, you can replace dormant working copies with readable files like:

```text
repo_my-project.md
repo_old-prototype.md
repo_chapel-ui.md
```

Each ledger file records useful Git metadata, including:

- repo name
- remote URL
- branch
- HEAD commit
- clean/dirty status
- recent commits
- timestamp of the snapshot

## Why?

Local development folders tend to become graveyards.

You hesitate to delete old repos because the directories themselves act as an inventory. But keeping every repo also means keeping `.git`, dependencies, build artifacts, virtual environments, `node_modules`, and other accumulated weight.

`repo-ledger` separates:

```text
active repo directory = operational presence
repo_*.md file         = historical memory
```

## Install

Clone this repo, then put the script somewhere on your PATH.

```sh
chmod +x bin/repo-ledger
```

Example:

```sh
mkdir -p ~/bin
cp bin/repo-ledger ~/bin/repo-ledger
```

Make sure `~/bin` is on your PATH.

## Usage

Run from anywhere inside a Git repo:

```sh
repo-ledger
```

By default, `repo-ledger` is strict. If the repo is clean and safe, this creates or appends to a file in the parent directory:

```text
../repo_{repo-name}.md
```

Example:

```text
~/dev/my-project/
~/dev/repo_my-project.md
```

To create a snapshot even when safety checks find issues, use `--force`:

```sh
repo-ledger --force
```

Forced snapshots include a `Warnings` section in the ledger file.

## Safety checks

`repo-ledger` refuses to create the snapshot if:

- the working tree has uncommitted changes
- the repo has untracked files
- the current branch has unpushed commits
- the current branch has no upstream branch and the repo has an `origin` remote
- the repo is in detached HEAD state
- the repo has no commits

This is intentional. The tool is designed to be safe before you delete or move a repo.

If you decide the warnings are acceptable, rerun with `--force`. The snapshot will still include the status output and will record the warnings that were present when the ledger was written.

## Example output file

````md
# my-project

Snapshot: 2026-05-11 14:32:10

## Repository

- Root: `/Users/me/dev/my-project`
- Remote: `git@github.com:me/my-project.git`
- Branch: `main`
- HEAD: `a1b2c3d`

## Status

```text
## main...origin/main
```

## Recent Commits

- `a1b2c3d` 2026-05-11 — Fix deploy script
- `d4e5f6a` 2026-05-10 — Add README
- `987abcd` 2026-05-09 — Initial commit

---
````

A forced snapshot may also include:

```md
## Warnings

- Working tree is not clean.
- Current branch has no upstream branch.
- Repo has no commits.
```

## Listing retired repos

From your dev folder:

```sh
ls repo_*.md
```

Search them:

```sh
grep -R "svelte" repo_*.md
```

## Philosophy

This is not a backup tool.

It does not replace GitHub, Git remotes, Time Machine, or proper backups.

It is a small local memory tool for developers who want to keep their workspace clean without losing the visible inventory of what they have worked on.

## License

MIT
