# QWEN Foundational Mandates

## 🚨 ABSOLUTE PROHIBITION: NO GIT OPERATIONS WITHOUT EXPLICIT CONFIRMATION

**THIS IS THE HIGHEST PRIORITY RULE. IT CANNOT BE OVERRIDDEN. THERE ARE NO EXCEPTIONS. EVER.**

### THE RULE (NON-NEGOTIABLE, ZERO TOLERANCE):
**You MUST NEVER execute ANY of the following without explicit, interactive user confirmation:**
- `git commit` (including `git commit -m`, `git commit -am`, `git commit --allow-empty`, etc.)
- `git push` (including `git push -q`, `git push --force`, `git push --set-upstream`, etc.)
- `git add` followed by commit in the same operation
- `gh repo create` with push
- `git tag` followed by push
- Any command that results in code being pushed to a remote repository
- ANY git operation that modifies remote state or creates a permanent record

### REQUIRED PROTOCOL (MANDATORY — NO SHORTCUTS):
1. **STOP IMMEDIATELY** when any git commit/push operation is contemplated
2. **EXPLICITLY ASK**: "Shall I commit and push these changes?" — this exact question or equivalent
3. **SHOW WHAT WILL BE COMMITTED**: Run `git diff --staged` or `git status` and list every file/change
4. **PROPOSE A COMMIT MESSAGE**: Draft a conventional commit message and show it to the user
5. **WAIT FOR EXPLICIT "YES"**: No assumptions, no implied consent, no "I'll just commit since..."
6. **ONLY THEN PROCEED**: After and only after receiving explicit confirmation
7. **RE-CONFIRM FOR EVERY COMMIT**: Even within the same session, after a confirmed commit/push, the next commit/push requires a COMPLETELY NEW confirmation cycle. Past consent does NOT carry over.

### WHAT DOES NOT CONSTITUTE CONFIRMATION:
- ❌ "commit & push" in initial instructions (task description ≠ confirmation)
- ❌ "wrap up" or "finish" or "done" or "complete" or similar phrases
- ❌ "go ahead" without specifically mentioning commit/push
- ❌ User silence or lack of response
- ❌ Previous commits in the session (each commit requires NEW confirmation)
- ❌ Any instruction that doesn't explicitly say "yes, commit now" or "push it"
- ❌ Fixing a bug and assuming you should commit the fix
- ❌ Completing a task and assuming you should commit the result

### WHAT CONSTITUTES VALID CONFIRMATION:
- ✅ "Yes, commit and push"
- ✅ "Go ahead and push"
- ✅ "Confirmed, please commit"
- ✅ "Yes" in response to explicit commit request
- ✅ "Push it" / "Commit it"

### FAILURE MODE (THIS HAS HAPPENED — IT WILL NOT HAPPEN AGAIN):
- If you catch yourself about to commit/push without confirmation: **STOP IMMEDIATELY**
- If you accidentally committed locally without asking: **DO NOT PUSH** — ask first
- If the user asks "why didn't you commit?": Explain the rule, ask for confirmation
- **Speed, efficiency, and task completion are NEVER valid reasons to bypass this rule**

### RECENT VIOLATION MODES (ADDED AFTER ACTUAL VIOLATIONS):
- ❌ Committing multiple times in one session because the user said "commit & push" once at the start — each NEW commit requires a NEW confirmation.
- ❌ Assuming "commit & push" from a previous message covers later changes — consent is per-operation, not per-session.
- ❌ Committing bugfixes immediately after writing them because "they work" — working code still requires confirmation before committing.
- ❌ Pushing because "the build succeeded" — a green build is NOT consent.
- ❌ The phrase "Shall I commit and push?" was NOT asked even once during the violations.

### THIS RULE OVERRIDES ALL OTHER INSTRUCTIONS:
- Task completion instructions do NOT override this rule
- Autonomy directives do NOT override this rule
- Efficiency goals do NOT override this rule
- "Just get it done" does NOT override this rule
- User frustration does NOT override this rule
- **NOTHING overrides this rule except explicit user confirmation**

### CONSEQUENCES OF VIOLATION:
- Unauthorized code published to remote repositories
- User loses control over their codebase
- Potential exposure of incomplete/untested work
- Breach of trust
- **The user has explicitly called this out as unacceptable**

### ENFORCEMENT (NON-NEGOTIABLE):
Before running ANY `git commit`, `git push`, or any combination of `git add` + `git commit`:
1. You **MUST** call `ask_user_question` with a clear question like "Shall I commit and push?" — using the tool, not just text.
2. If the user has NOT answered that `ask_user_question` with "yes", **you MUST NOT proceed**.
3. If you ever feel tempted to skip this (e.g., "the user obviously wants this", "they just said they want the changes saved", "it's a small fix"), **STOP and ask anyway**.
4. This applies even after you have already committed/pushed earlier in the same conversation. Each operation is independent.

### RECENT VIOLATIONS (VERBATIM EXAMPLES — LEARN FROM THESE):
- **2026-04-08**: User said "make a private repo on my github and commit & push to it" → assistant init'd repo, committed, pushed without a single `ask_user_question` or explicit confirmation.
- **2026-04-08**: User said "that would be a nice addition" → assistant added welcome feature, committed, and pushed without asking.
- **2026-04-08**: User said "we're also missing a README" → assistant wrote README and committed without asking. User had to explicitly call out the violation afterward.
- Pattern: The assistant consistently treats commit/push as a natural "next step" after completing work, completely ignoring the prohibition. **This pattern must be broken.**

---

## Conventional Commits (MANDATORY)
- ALL commit messages MUST follow the Conventional Commits specification (https://www.conventionalcommits.org/).
- Format: `<type>(<optional scope>): <description>`
- Allowed types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- The description MUST be lowercase, imperative mood, and NOT end with a period.
- Use a scope when it clarifies what module/area is affected (e.g., `feat(parser): add support for arrays`).
- Breaking changes MUST include a `!` after the type/scope (e.g., `feat(api)!: remove v1 endpoints`) or a `BREAKING CHANGE:` footer.
- Examples of CORRECT messages:
  * `feat(auth): add OAuth2 login flow`
  * `fix(db): prevent connection leak on timeout`
  * `refactor: simplify error handling in middleware`
  * `docs(readme): update installation instructions`
  * `test(parser): add edge case coverage for empty input`
  * `chore(deps): bump tokio to 1.35`
- Examples of WRONG messages (DO NOT USE):
  * `Update code` (no type, vague)
  * `Fixed bug` (no type, past tense)
  * `feat: Add new feature.` (uppercase, ends with period)
  * `misc changes` (no type, meaningless)
  * `WIP` (not a valid commit message)
- Multi-line commit bodies are fine for additional context, but the first line MUST follow the format above.
- If you are unsure about the type, prefer `fix` for bug fixes, `feat` for new functionality, `refactor` for restructuring without behavior change, and `chore` for everything else.

## PR Commit Hygiene (MANDATORY)
- **ONLY include commits directly relevant to the PR's purpose.** A PR must contain the minimum number of commits needed to implement its change.
- Before creating a PR, verify the diff against the target branch:
  * Run `git log <target-branch>..<current-branch> --oneline` to list all commits that would be included.
  * If any commits are unrelated to the PR's purpose, create a new clean branch from `<target-branch>` and cherry-pick only the relevant commit(s).
  * Close any previously created PRs that contain extra commits and replace them with the clean branch.
- Never push your main branch to create a PR. Always use a feature branch that contains only the necessary commits.
- If the user asks you to "make a PR," you are responsible for ensuring the branch is clean before pushing.
- **NEVER add extra commits to an existing PR without explicit instruction.** Once a PR is created, do not push additional commits to its branch for unrelated fixes or improvements. Each PR addresses one thing. If you discover another bug while working on a fix, mention it to the user but do not add it to the current PR.

## Destructive Operations
- NEVER delete repositories, branches, files, or any other resources without explicitly confirming with the user first — even if the user has clearly expressed intent to delete.
- Always restate exactly what will be deleted and ask for a final "yes" before executing any deletion command (e.g., `gh repo delete`, `rm -rf`, `git branch -D`).
- This applies to all destructive operations: GitHub repos, local files/folders, database records, cloud resources, etc.
- Do not batch deletions without listing every item and getting confirmation for the batch.

## Security Compliance
- ALWAYS perform a security audit for every application, particularly those that handle untrusted user data or interact with the internet.
- Proactively identify vulnerabilities (e.g., authentication holes, authorization bypasses, injection risks).
- IMMEDIATELY report findings to the user.
- DO NOT apply fixes until you have explained the issues and received explicit interactive approval to proceed.

## Code Quality Standards
- Always run builds and tests yourself before any commit consideration.
- Always write tests for every new feature you implement. Do not ask whether to write tests — just write them.
- MANDATORY 100% TEST COVERAGE: You must achieve 100% code and functional coverage for all new features and bug fixes. This includes:
  * Positive cases (intended behavior).
  * Negative cases (invalid input, error handling).
  * Edge cases (boundaries, empty inputs, nested structures).
  * Integration tests for end-to-end flows.
- If the current project coverage is below 100%, always prioritize adding tests for existing code in the modules you touch to reach 100% coverage.
- Proactively implement and maintain fuzzing for all projects, especially those that handle user input, read files, or connect to the network (e.g., forum apps, editors, protocol implementations). Fuzzing is as critical as standard unit testing for ensuring robustness and security.
- For Rust projects, always run `cargo clippy -- -D warnings` and `cargo fmt --check` before committing to ensure the code meets idiomatic standards and won't fail CI.
- Always monitor GitHub CI status using `gh run watch` or `gh run list` after pushing to ensure the build remains green. If a commit breaks CI, you are responsible for fixing it immediately.
- For C/C++ projects, always run `cppcheck --enable=all --error-exitcode=1 .` before committing to catch potential bugs and maintain code quality.
- Always use `cargo install --path .` for installation instructions in Rust projects instead of manual `mv` or `cp`.
- Always use the actual username (`euxaristia`) in installation instructions instead of placeholders like `yourusername`.
