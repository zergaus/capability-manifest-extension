# Capability Manifest Extension Repository Instructions

Global Codex instructions remain authoritative unless a more specific repository or directory instruction applies.

## Conditional GitHub publish gate

Apply this gate only when a task stages, commits, pushes, opens a PR, or otherwise publishes repository changes.

- Staging, branch creation, successful `git push`, or `Everything up-to-date` does not by itself prove that intended files were published.
- Before commit, confirm repository-local author identity; prefer `git config --local` when identity is missing unless a global change is explicitly intended.
- Stage only intended paths; do not use broad staging when unrelated work may exist. Force-add ignored files only when the exact ignored path is intentionally part of the change.
- Do not declare publication complete until the commit succeeds, the worktree is clean, local `HEAD` equals upstream, and remote comparison or file inspection confirms the intended commit/files.
- Treat line-ending policy as correctness-critical when files participate in byte-exact or hash-based evidence.
