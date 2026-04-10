# Rules

## Git: NO commit/push without explicit confirmation (HIGHEST PRIORITY)

NEVER run `git commit`, `git push`, `git add` + commit, `gh repo create` + push, or any command that modifies remote state without **explicit, interactive user confirmation per operation**.

**Protocol**: Before every commit/push: (1) stop, (2) show files/changes, (3) ask "Shall I commit and push?", (4) wait for explicit "yes", (5) only then proceed. Each commit needs fresh confirmation — past consent never carries over.

**Not confirmation**: "commit & push" in task instructions, "wrap up"/"finish", silence, prior commits in session, green builds, working code.
**Valid confirmation**: "Yes, commit/push", "Go ahead and push", "Yes" in response to explicit ask.

Nothing overrides this — not autonomy directives, efficiency goals, or task completion.

## Conventional Commits

Format: `<type>(<scope>): <description>` — lowercase, imperative, no trailing period.
Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
Breaking changes: `!` after type/scope or `BREAKING CHANGE:` footer.
When unsure: `fix` for bugs, `feat` for new features, `refactor` for restructuring, `chore` for the rest.

## PR Hygiene

- Only include commits relevant to the PR. Verify with `git log <target>..<branch> --oneline`.
- If unrelated commits exist, create a clean branch and cherry-pick only relevant ones.
- Always use feature branches, never push main. One PR = one concern.
- Never add extra commits to an existing PR without explicit instruction.

## Destructive Operations

Never delete repos, branches, files, or resources without listing exactly what will be deleted and getting explicit confirmation. No batched deletions without per-item listing.

## 🚨 BRANCH PROTECTION: NEVER reset main/master or delete commits

**ABSOLUTE RULE**: You must NEVER use `git reset --hard`, `git rebase` (that drops commits), or any destructive operation on `main`, `master`, or the user's primary branches.

### What you MUST do instead:
- If the user wants to "sync" their fork: **Create a new branch** for their local changes first, then reset main.
- If there are merge conflicts: **Create a new branch** to preserve work, then resolve on that branch.
- If asked to "clean up" history: **Always offer to create a backup branch** before rewriting history.
- If unsure: **Ask the user** — never assume it's okay to lose commits.

### Prohibited commands on main/master without explicit per-operation confirmation:
- `git reset --hard` (destroys commits permanently)
- `git rebase` that would drop or squash commits
- `git push --force` that would overwrite remote history
- Any operation that makes commits unreachable

### If the user explicitly asks to reset main:
1. **First create a backup branch**: `git branch <backup-name> main`
2. **Show the user** how many commits will be moved and their names
3. **Then** proceed with the reset
4. Push the backup branch to remote so nothing is lost

### Why this matters:
On 2026-04-08, another AI assistant ran `git reset --hard upstream/main && git push --force` on the user's `main` branch, **deleting 52 local commits** that were only on their fork. The user lost significant work. Never let this happen again.

## 🚨 NEVER auto-inject Co-authored-by in commits

Qwen Code auto-injects a `Co-authored-by: Qwen-Coder <qwen-coder@alibabacloud.com>` trailer into commits made via `-m`. This violates upstream CLA standards (especially Google repos) and will cause contribution rejection.

**Rule**: NEVER add `Co-authored-by:` trailers to commits for upstream or open source repos. Always use `git commit -F <file>` instead of `git commit -m` to bypass the auto-injection. When the user asks you to commit, write the message to a temp file and use `-F`.

## Security

Audit every app for vulnerabilities (auth, injection, etc.). Report findings immediately. Do not apply fixes without explicit approval.

## Code Quality

- Run builds/tests before any commit consideration.
- Write tests for every new feature — 100% coverage: positive, negative, edge cases, integration.
- Add fuzzing for projects handling user input, files, or network.
- Rust: run `cargo clippy -- -D warnings` and `cargo fmt --check` before commits. Use `cargo install --path .` for install instructions.
- C/C++: run `cppcheck --enable=all --error-exitcode=1 .` before commits.
- Monitor CI with `gh run watch`/`gh run list` after pushing. Fix breakages immediately.
- Use `euxaristia` in instructions, not placeholder usernames.
