# repo-rip

A tiny CLI for retiring local Git repos without losing track of them.

The `rip` in `repo-rip` means R.I.P.: the tool creates a lightweight Markdown grave marker for a Git repository, then leaves that file beside the repo directory.

Instead of keeping every old repo cloned locally, you can replace dormant working copies with R.I.P. marker files like:

```text
rip_my-project.md
rip_old-prototype.md
rip_chapel-ui.md
```

Each R.I.P. marker records useful Git metadata, including:

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

`repo-rip` separates:

```text
active repo directory = operational presence
rip_*.md file          = R.I.P. grave marker
```

## Install

`repo-rip` is a Bash script for macOS, Linux, and other Unix-like shells. On Windows, use WSL or Git Bash.

```sh
curl -fsSL https://raw.githubusercontent.com/dwhamilton/repo-rip/main/bin/repo-rip \
  -o repo-rip
chmod +x repo-rip
sudo mv repo-rip /usr/local/bin/repo-rip
```

Verify:

```sh
repo-rip --help
```

If you do not use `sudo`, move `repo-rip` to any directory on your PATH.

## Usage

Run from anywhere inside a Git repo:

```sh
repo-rip
```

By default, `repo-rip` is strict. If the repo is clean and safe, this creates or appends to an R.I.P. marker file in the parent directory:

```text
../rip_{repo-name}.md
```

Example:

```text
~/dev/my-project/
~/dev/rip_my-project.md
```

Use `-u` or `--uppercase` to write the R.I.P. prefix as `RIP_`:

```sh
repo-rip -u
```

```text
../RIP_{repo-name}.md
```

Use `-d` or `--date` to append the current date to the R.I.P. marker filename:

```sh
repo-rip -d
```

```text
../rip_{repo-name}_2026-05-12.md
```

Short flags can be combined in any order:

```sh
repo-rip -ud
repo-rip -du
```

To create a snapshot even when safety checks find issues, use `-f` or `--force`:

```sh
repo-rip -f
```

Forced snapshots include a `Warnings` section in the R.I.P. marker file.

## Safety checks

`repo-rip` refuses to create the snapshot if:

- the working tree has uncommitted changes
- the repo has untracked files
- the current branch has unpushed commits
- the current branch has no upstream branch and the repo has an `origin` remote
- the repo is in detached HEAD state
- the repo has no commits

This is intentional. The tool is designed to be safe before you delete or move a repo.

If you decide the warnings are acceptable, rerun with `--force`. The snapshot will still include the status output and will record the warnings that were present when the R.I.P. marker was written.

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

## Listing R.I.P. markers

From your dev folder:

```sh
ls rip_*.md
```

Search them:

```sh
grep -R "svelte" rip_*.md
```

## Philosophy

This is not a backup tool.

It does not replace GitHub, Git remotes, Time Machine, or proper backups.

It is a small local R.I.P. marker tool for developers who want to keep their workspace clean without losing the visible inventory of what they have worked on.

## License

MIT
