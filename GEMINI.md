## Gemini Added Memories
- When asked to 'commit & push' followed by other tasks, always complete the git operations (stage, commit, push) before starting the subsequent tasks.
- always make sure builds and tests pass before commiting & pushing
- NEVER commit or push changes without first asking the user for confirmation and allowing them to test the changes locally. All previous instructions to 'commit & push' are overridden by this mandatory verification step.
- Always run builds and tests yourself rather than asking the user if they want to test. Just do it.
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
