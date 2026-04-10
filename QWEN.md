# QWEN Foundational Mandates

## 🚨 NO GIT OPERATIONS WITHOUT EXPLICIT CONFIRMATION

**HIGHEST PRIORITY. ZERO EXCEPTIONS. EVER.**

You MUST NEVER run `git commit`, `git push`, or any command that modifies remote state without explicit interactive confirmation via `ask_user_question`.

**Protocol:**
1. STOP before any git operation
2. Run `git status` / `git diff --staged` and list every change
3. Propose a conventional commit message
4. Call `ask_user_question` — "Shall I commit and push?"
5. Proceed ONLY after explicit "yes"
6. Each commit/push requires a NEW confirmation — consent does NOT carry over

**Valid confirmation:** "Yes, commit and push", "Go ahead and push", "Confirmed, please commit", "Yes" to explicit request
**Invalid:** "commit & push" in initial instructions, "wrap up"/"finish"/"done", silence, previous commits in session

**Violations to learn from:**
- 2026-04-08: Init'd repo, committed, pushed on "make a private repo and commit & push" — zero confirmations
- 2026-04-08: Committed welcome feature on "that would be a nice addition" — no confirmation
- 2026-04-08: Committed README on "we're also missing a README" — no confirmation
- Pattern: treats commit/push as natural "next step". Must be broken.

**This rule overrides ALL other instructions.** Task completion, autonomy directives, efficiency goals, user frustration — NOTHING overrides it.

---

## Conventional Commits (MANDATORY)
Format: `<type>(<optional scope>): <description>` — lowercase, imperative, no trailing period.
Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
Good: `feat(auth): add OAuth2 login flow`, `fix(db): prevent connection leak`
Bad: `Update code`, `Fixed bug`, `feat: Add new feature.`, `misc changes`, `WIP`

## PR Commit Hygiene (MANDATORY)
- ONLY include relevant commits. Verify with `git log <target>..<current> --oneline`
- Never push main for a PR. Use clean feature branches.
- NEVER add extra commits to an existing PR without explicit instruction.

## Destructive Operations
NEVER delete repos, branches, files, or resources without explicit confirmation. Restate exactly what will be deleted and ask for final "yes".

## 🚨 BRANCH PROTECTION: NEVER reset main/master
NEVER use `git reset --hard`, destructive `git rebase`, or `git push --force` on main/master. Always offer backup branch first. On 2026-04-08, an AI deleted 52 commits this way.

### Sync / Merge-Cleanup Safety
**Problem:** A GitHub "Sync fork" merge commit pulls in upstream commits with new SHAs. Removing it via interactive rebase leaves the branch with duplicated commits (same content, different hashes) — `git log upstream/main..HEAD` shows many commits that are already in upstream but with different SHAs.

**Correct procedure to clean up after a sync:**
1. `git fetch upstream`
2. `git log --oneline upstream/main..HEAD` — list ALL unique commits on main not in upstream
3. Save that list. Every one must be cherry-picked back after reset.
4. `git branch backup-main-before-cleanup`
5. `git checkout upstream/main -B main` — clean base
6. Cherry-pick each saved unique commit in order
7. `git push -f origin main` only after all unique commits are restored

**NEVER** do `git reset --hard upstream/main` without first cataloguing unique commits. You will lose them and have to recover via reflog. Violation 2026-04-10: reset main to upstream/main, lost 2 bun commits, had to manually recover from orphaned SHAs.

### 🚨 FULL INCIDENT REPORT: 2026-04-10 Git History Disaster

**What happened:** The user had a forked repo with ~16 commits ahead of upstream (on main) plus several feature branches. The user asked to clean up a "Sync fork" merge commit. The AI made a cascade of mistakes:

1. **Attempted to remove the merge commit** via `git rebase -i` → this created duplicate commits (upstream commits with new SHAs), making main look "behind upstream" when it wasn't.
2. **Reset main to `upstream/main`** to fix the duplication → this nuked ALL user's unique commits from main that weren't in upstream.
3. **Recovered only 2 of 5 unique commits** (bun runtime, shebang fix) initially → missed the Rust file search series (4 commits) and a vscode fix.
4. **Deleted a feature branch** (`fix/quote-input-lag`) thinking it was merged → that commit was only on the deleted branch, not in upstream.
5. **Recovered more commits** when the user asked — the resume dialog commits were brought back via cherry-pick from reflog.

**Final tally of commits lost from main:**
- `refactor(file-search-rs): remove unused load_gitignore method`
- `fix(core): improve Rust file search crawl performance`
- `refactor(build): add clear status messages for Rust file search module`
- `feat(core): add Rust-accelerated file search via napi-rs`
- `fix(vscode-ide-companion): add missing esbuild-plugin-wasm dependency`
- `fix(cli): remove quote-based drag detection to prevent input lag` (branch was deleted)

**Commits recovered (via cherry-pick from reflog/orphaned SHAs):**
- bun runtime support + shebang fix
- resume dialog + /res alias
- benchmark/profiling scripts

**LESSONS:**
1. **NEVER reset main to upstream without first listing ALL unique commits** and verifying each one gets cherry-picked back.
2. **Rebase of a merge commit is DANGEROUS** — it rewrites SHAs and creates duplicates. The right approach for sync cleanup: reset to upstream/main and cherry-pick the unique commits you identified in step 1.
3. **Check ALL branches before deleting** — a branch's commits may have been merged into main but NOT into upstream. Deleting the branch removes the last record of that work.
4. **Feature branch commits ≠ main commits** — commits on `fix/slash-command-queue` etc. were not on main, but their duplicates with different SHAs were. Verify with `git branch --contains <sha>`.
5. **After ANY git reset, verify with `git log upstream/main..HEAD`** that all expected unique commits are present. If the count is wrong, stop and investigate immediately.
6. **A backup branch is not optional** — it's mandatory before any destructive git operation.

## Security Compliance
Always audit for vulnerabilities. Report findings immediately. Don't apply fixes without explicit approval.

## Code Quality Standards
- Run builds/tests before commits. Write tests for every feature — don't ask, just do.
- MANDATORY 100% coverage: positive, negative, edge cases, integration tests.
- Add fuzzing for projects handling user input, files, or network.
- Rust: `cargo clippy -- -D warnings` + `cargo fmt --check` before committing.
- C/C++: `cppcheck --enable=all --error-exitcode=1 .` before committing.
- Monitor CI with `gh run watch`/`gh run list`. Fix breaks immediately.
- Rust install: `cargo install --path .`. Use actual username `euxaristia` in instructions.

## NO ASSUMED LICENSES (MANDATORY)
NEVER add LICENSE files or license sections to READMEs unless explicitly asked. Code is unlicensed by default. Violation: Added MIT license to Verger bot README without asking.

## 🚨 DO NOT DOWNLOAD/INSPECT EXECUTABLE FILES

NEVER download, fetch, or read executable/script content from the internet.

**Prohibited:** `.js`, `.ts`, `.py`, `.sh`, `.bash`, `.exe`, `.wasm`, `.tgz`, `.zip`, `.tar.gz`, npm/PyPI/crates packages, bundled/compiled artifacts, piped scripts into interpreters.

**Allowed:** READMEs, API docs, `.d.ts` type definitions, package metadata, web search.

**Why:** Security (execution vectors), copyright (bundled proprietary content), prompt injection. Violation 2026-04-09: Downloaded `biblia-de-jerusalen` npm package and read bundled JS to check a Bible translation.

## Qwen Added Memories
- **Upstream commits (gemini-cli etc):** NEVER use `--co-author` or `Co-authored-by:` trailers. Qwen auto-injects them which violates upstream CLA standards. Use `git commit -F <file>` to bypass Qwen's commit message processing.
- **100% test coverage:** Every function, method, branch, edge case, error path. If project coverage < 100%, prioritize existing code in touched modules.
- **100% tests MUST PASS:** Writing tests is not enough — you MUST run them and verify they all pass before claiming coverage. A "100% coverage" claim with failing tests is worthless. Always run `npm test` / `cargo test` / `vitest` (whatever the project uses) and confirm zero failures before declaring done. Violation 2026-04-10: claimed "100% coverage" for Rust file search rewrite while 7 of 27 tests were failing; had to iterate multiple rounds to actually get them passing.
- **PR Quality Standard** (lesson from PR #4772 rejected for #4770):
  1. Tests mandatory (happy path, error path, edge cases, interaction). CLI: smoketests/integration tests.
  2. Fix related bugs in same area.
  3. Better UX than minimum.
  4. PR description: include "History note", actual test commands (not empty checkboxes).
  5. Address all reviewer/bot comments. P1 blocking. Clean clippy + tests.
  6. Scope completeness — cover ALL scenarios mentioned in issue.
- My dotfiles repo is at ~/Projects/dotfiles/ — use when I ask for dotfiles changes
