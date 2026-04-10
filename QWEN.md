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
