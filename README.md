# tmux delight

A small collection of delightful tmux helpers.

## Table of Contents

- [tmux-session](#tmux-session) — Interactive layout selector for tmuxp sessions
- [tmux-worktree](#tmux-worktree) — Git worktree + tmux session manager

---

## tmux-session

Interactive helper to select a tmuxp layout from a directory via `fzf`, then create a new tmux session or attach to an existing one.

### Usage

```
tmux-session --layouts-dir <path>

# Example:
tmux-session --layouts-dir ~/.config/tmuxp
```

### What it does

- Scans a directory for tmuxp layout files (`.yaml` / `.yml`)
- Presents an interactive `fzf` picker with file preview
- Automatically hides template files that are referenced by overlay files
- If the session already exists, attaches to it instead of recreating
- Supports two file types:
  1. **Full layouts** — standard tmuxp files with `session_name` or `windows` keys
  2. **Overlay files** — lightweight files with `template` and `env` keys that reference a base template

### Overlay Files

Overlay files let you reuse a single template layout across multiple projects. An overlay file specifies environment variables and points to a template:

```yaml
# my-project.yaml (overlay)
env:
  - SESSION_NAME=my_project
  - START_DIR=~/projects/my_project
template: ./default_template.yaml
```

The referenced template uses environment variable substitution:

```yaml
# default_template.yaml (template)
session_name: ${SESSION_NAME}
start_directory: ${START_DIR}
windows:
  - window_name: editor
    panes:
      - nvim
  - window_name: shell
    panes:
      - ""
```

Template files referenced by overlays are automatically excluded from the `fzf` selection list.

### Prerequisites

- Tools: `tmux`, `tmuxp`, `fzf`, `yq`

### Tips

- Use overlay files to maintain a single layout template for many projects
- See `layout_examples/` for sample layouts and overlays

---

## tmux-worktree

Interactive helper to spin up (or re-attach to) a git worktree and launch a predefined tmux layout via `tmuxp`.

### Usage

```
tmux-worktree <git-worktree-root> <tmuxp-layout-yaml>

# Example:
tmux-worktree ~/repos/my-bare-repo ~/.config/tmuxp/worktrees/web-api.yaml
```

### What it does

- Lists existing worktree directories under `<git-worktree-root>/worktrees` and lets you select (or type a new) branch via `fzf`
- Reuses an existing worktree or creates a new one:
  - If the remote branch exists, attaches or creates from `origin/<branch>`
  - Otherwise creates from detected default branch (`origin/HEAD`, remote HEAD report, else `main`, else `master`)
- Normalizes the tmux session name by replacing `/` with `_`
- Exports `GIT_WORKTREE=<session_name>` and loads the provided tmuxp layout file
- Expands a leading `~/` in the layout path safely (no eval)

### Prerequisites

- Tools: `git`, `tmux`, `tmuxp`, `fzf`
- Env var: `TMUX_CONFIG` must be set
- Repo form: bare repo (or a repo directory containing a `worktrees/` folder)

### Tips

- Type a brand-new branch name directly in the `fzf` prompt—no pre-creation needed
- Reference `GIT_WORKTREE` inside your tmuxp layout for dynamic naming/paths
- Create a shell alias if you always use the same layout
