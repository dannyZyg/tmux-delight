AGENT PLAYBOOK (tmux-conductor)

1. Stack: Bash scripts only. No build system; ensure executables keep shebangs. No tests present; if adding tests prefer shellcheck + bats.
2. Commands:
   - Lint all: shellcheck init-worktree tmux-worktree tmux-sessionizor
   - Lint single file: shellcheck <file>
   - Run one bats test (if added): bats -f <name_regex> tests
3. Executable policy: Always chmod +x new scripts. Preserve LF endings. Shebangs: env bash unless portability to /bin/bash is required; current files use env bash except tmux-worktree which uses /bin/bash (keep as-is).
4. Imports/Deps: Avoid sourcing unless necessary. External tools relied on: git, tmux, fzf, pyenv, invoke (inv), requirements helper. Check availability before use; fail fast with clear message.
5. Style: set -euo pipefail for new scripts (existing ones can be incrementally updated). Quote all variable expansions except in [[ ]] tests or when intentional word splitting (none now). Use UPPER_CASE for exported CONSTs, lower_snake for locals.
6. Functions: Factor repeated logic into small functions at top; main entry at bottom with main "$@" pattern when adding complexity.
7. Naming: scripts use hyphen-separated names; keep consistent. Variables: SESSION_NAME, WORKTREE_PATH patterns already established—follow them.
8. Error handling: Guard required env vars (example TMUX_CONFIG) with explicit message then exit 1. For commands whose failure is acceptable, append || true with comment.
9. User interaction: fzf usage: always validate empty selection (already done). For additions keep non-interactive fallback.
10. Git operations: Prefer explicit branches (master currently referenced—confirm default; consider main). Validate remote branch via git ls-remote before creating worktree.
11. Performance: Use find -mindepth 1 -maxdepth 1 -type d then basename (current OK). Avoid subshell loops if simple command substitution suffices.
12. Portability: Assume macOS/Linux. Avoid GNU-only flags unless checked. Use POSIX constructs where possible, but [[ ]] acceptable under bash.
13. Tooling extensions: If adding Python helpers, pin pyenv local version, create venv in .venv, run inv tasks as in init-worktree.
14. Logging: Echo concise actions; prefix errors with "Error:". Avoid noisy stdout for success paths.
15. Adding tests: Place under tests/*.bats. Keep each test independent; arrange fixtures in tests/fixtures.
16. Shellcheck directives: Prefer fixing warnings; if disabling add justification: # shellcheck disable=SCXXXX -- reason.
17. Security: Never eval untrusted input. Sanitize selections (basename already). Quote paths.
18. Future extension: Consider a Makefile with targets lint, test, format (noop) if complexity grows.
19. AI agent etiquette: Do not introduce other languages without justification. Keep AGENTS.md <= 25 lines.
20. Update this file when introducing tests, new languages, or build tooling.
