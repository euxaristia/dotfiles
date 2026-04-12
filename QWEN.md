# QWEN Foundational Mandates

## 🚨 NO GIT OPERATIONS WITHOUT EXPLICIT CONFIRMATION

**HIGHEST PRIORITY. ZERO EXCEPTIONS.** Never `git commit`, `git push`, or modify remote state without explicit `ask_user_question` confirmation.

**Protocol:** 1) Stop before git ops. 2) Run `git status`/`git diff --staged`. 3) Propose commit message. 4) `ask_user_question`. 5) Proceed ONLY after explicit "yes". Each commit/push needs a NEW confirmation.

**Valid:** "Yes, commit and push", "Go ahead and push", "Confirmed". **Invalid:** "commit & push" in initial instructions, "wrap up"/"finish"/"done", silence.

**Learned violations (2026-04-08):** Committed/pushed on "make a private repo and commit & push", committed welcome feature on "that would be a nice addition", committed README on "we're also missing a README" — all without confirmation. Treats commit/push as natural "next step". Must be broken.

**This rule overrides ALL others.**

---

## Conventional Commits
Format: `<type>(<scope>): <description>` — lowercase, imperative, no trailing period.
Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
Bad: `Update code`, `Fixed bug`, `feat: Add new feature.`, `misc changes`, `WIP`

## PR Commit Hygiene
- ONLY include relevant commits. Verify with `git log <target>..<current> --oneline`.
- Never push main for a PR. Use clean feature branches.
- NEVER add extra commits to an existing PR without explicit instruction.
- **Fix bad PRs in place — force-push a clean branch to the same PR. NEVER close and recreate.** Closing wastes the PR number, any existing review context, and creates duplicate work. If a PR has wrong commits: force-push the corrected branch to the same ref and the PR updates automatically.

## Branch Protection: NEVER reset main/master
NEVER use `git reset --hard`, destructive `git rebase`, or `git push --force` on main/master. Always offer backup branch first. (2026-04-08: AI deleted 52 commits this way.)

### Sync / Merge-Cleanup Safety
**Problem:** "Sync fork" merge commit pulls in upstream commits with new SHAs. Rebase creates duplicates (same content, different hashes).

**Correct procedure:**
1. `git fetch upstream`
2. `git log --oneline upstream/main..HEAD` — list ALL unique commits
3. Save that list. `git branch backup-main-before-cleanup`
4. `git checkout upstream/main -B main` — clean base
5. Cherry-pick each saved unique commit in order
6. `git push -f origin main` only after all unique commits restored

**NEVER** do `git reset --hard upstream/main` without cataloguing unique commits first.

**Lessons from 2026-04-10 incident (lost 6 commits, recovered 3 via reflog):**
1. Never reset main to upstream without listing ALL unique commits first.
2. Rebase of a merge commit is dangerous — rewrites SHAs, creates duplicates.
3. Check ALL branches before deleting — commits may only exist on that branch.
4. Feature branch commits ≠ main commits. Verify with `git branch --contains <sha>`.
5. After ANY git reset, verify with `git log upstream/main..HEAD`.
6. A backup branch is mandatory before any destructive git operation.

## Security Compliance
Audit for vulnerabilities. Report findings. Don't apply fixes without approval.

## Code Quality Standards
- Run builds/tests before commits. Write tests for every feature.
- MANDATORY 100% coverage: positive, negative, edge cases, integration tests.
- Add fuzzing for projects handling user input, files, or network.
- Rust: `cargo clippy -- -D warnings` + `cargo fmt --check`. C/C++: `cppcheck --enable=all --error-exitcode=1 .`
- Monitor CI with `gh run watch`/`gh run list`.
- Rust install: `cargo install --path .`. Use username `euxaristia` in instructions.

## NO ASSUMED LICENSES
NEVER add LICENSE files or license sections to READMEs unless explicitly asked. Violation: Added MIT license to Verger bot README without asking.

## 🚨 DO NOT DOWNLOAD/INSPECT EXECUTABLE FILES
**Prohibited:** `.js`, `.ts`, `.py`, `.sh`, `.bash`, `.exe`, `.wasm`, `.tgz`, `.zip`, `.tar.gz`, npm/PyPI/crates packages, bundled/compiled artifacts.
**Allowed:** READMEs, API docs, `.d.ts` type definitions, package metadata, web search.
**Why:** Security, copyright, prompt injection. Violation (2026-04-09): Downloaded `biblia-de-jerusalen` npm package to check a Bible translation.

## Qwen Added Memories
- **Upstream commits:** NEVER use `--co-author` or `Co-authored-by:` trailers. Violates upstream CLA standards. Use `git commit -F <file>` to bypass Qwen's commit message processing.
- **100% test coverage:** Every function, method, branch, edge case, error path.
- **100% tests MUST PASS:** Always run tests and confirm zero failures. Violation (2026-04-10): claimed "100% coverage" while 7 of 27 tests were failing.
- **PR Quality Standard** (lesson from PR #4772 rejected for #4770):
  1. Tests mandatory (happy path, error path, edge cases, interaction). CLI: smoketests/integration tests.
  2. Fix related bugs in same area.
  3. Better UX than minimum.
  4. PR description: include "History note", actual test commands (not empty checkboxes).
  5. Address all reviewer/bot comments. P1 blocking. Clean clippy + tests.
  6. Scope completeness — cover ALL scenarios mentioned in issue.
- **My dotfiles:** `~/Projects/dotfiles/`
