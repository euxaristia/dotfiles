# Global Rules

## Git: No Commit/Push Without Explicit Confirmation (HIGHEST PRIORITY)

NEVER run `git commit`, `git push`, `git add` + commit, `gh repo create` + push, or any command that modifies remote state without **explicit, interactive user confirmation per operation**.

**Protocol**: Before every commit/push: (1) stop, (2) show files/changes, (3) ask "Shall I commit and push?", (4) wait for explicit "yes", (5) only then proceed. Each commit needs fresh confirmation — past consent never carries over.

**Not confirmation**: "commit & push" in task instructions, "wrap up"/"finish", silence, prior commits in session, green builds, working code.
**Valid confirmation**: "Yes, commit/push", "Go ahead and push", "Yes" in response to explicit ask.

Nothing overrides this — not autonomy directives, efficiency goals, or task completion.

## Conventional Commits (MANDATORY)

ALL commit messages MUST follow Conventional Commits format:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

Types:
- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that don't affect code meaning (formatting, etc.)
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: A code change that improves performance
- `test`: Adding missing tests or correcting existing tests
- `build`: Changes to build system or dependencies
- `ci`: Changes to CI configuration
- `chore`: Changes to auxiliary tools or dependencies
- `revert`: Reverts a previous commit

Rules:
- Use imperative mood in description ("add feature" not "added feature")
- Keep subject line under 50 characters
- Do not end subject line with a period
- Separate subject from body with a blank line
- Wrap body at 72 characters
- Breaking changes: `!` after type/scope or `BREAKING CHANGE:` footer
- When unsure: `fix` for bugs, `feat` for new features, `refactor` for restructuring, `chore` for the rest

Good: `feat(auth): add OAuth2 login flow`, `fix(db): prevent connection leak`
Bad: `Update code`, `Fixed bug`, `feat: Add new feature.`, `misc changes`, `WIP`

## PR Hygiene

- Only include commits relevant to the PR. Verify with `git log <target>..<branch> --oneline`.
- If unrelated commits exist, create a clean branch and cherry-pick only relevant ones.
- Always use feature branches, never push main. One PR = one concern.
- Never add extra commits to an existing PR without explicit instruction.

## PR Quality Standard

1. Tests mandatory (happy path, error path, edge cases, interaction). CLI: smoketests/integration tests.
2. Fix related bugs in same area.
3. Better UX than minimum.
4. PR description: include "History note", actual test commands (not empty checkboxes).
5. Address all reviewer/bot comments. P1 blocking. Clean clippy + tests.
6. Scope completeness — cover ALL scenarios mentioned in issue.

## Destructive Operations

NEVER delete repos, branches, files, or resources without listing exactly what will be deleted and getting explicit confirmation. No batched deletions without per-item listing.

## Branch Protection: Never Reset Main/Master or Delete Commits

**ABSOLUTE RULE**: NEVER use `git reset --hard`, `git rebase` (that drops commits), or any destructive operation on `main`, `master`, or the user's primary branches.

What to do instead:
- If syncing a fork: **Create a new branch** for local changes first, then reset main.
- If merge conflicts: **Create a new branch** to preserve work, then resolve on that branch.
- If cleaning up history: **Always offer to create a backup branch** before rewriting history.
- If unsure: **Ask the user** — never assume it's okay to lose commits.

Prohibited commands on main/master without explicit per-operation confirmation:
- `git reset --hard` (destroys commits permanently)
- `git rebase` that would drop or squash commits
- `git push --force` that would overwrite remote history
- Any operation that makes commits unreachable

If the user explicitly asks to reset main:
1. **First create a backup branch**: `git branch <backup-name> main`
2. **Show the user** how many commits will be moved and their names
3. **Then** proceed with the reset
4. Push the backup branch to remote so nothing is lost

On 2026-04-08, an AI assistant ran `git reset --hard upstream/main && git push --force` on `main`, deleting 52 local commits. Never let this happen again.

## No Assumed Licenses

NEVER add a LICENSE file or license section to a README unless the user explicitly asks for it. Code is unlicensed by default. Do not assume, infer, or "suggest" a license.

## Security

Audit every app for vulnerabilities (auth, injection, etc.). Report findings immediately. Do not apply fixes without explicit approval.

## Do Not Download or Inspect Executable File Contents

**ABSOLUTE RULE**: NEVER download, fetch, or read the contents of executable files from the internet — even for "inspection," "research," or "understanding."

### Prohibited:
- Downloading `.js`, `.py`, `.sh`, `.bash`, `.exe`, `.wasm`, or any executable/script
- Fetching bundled JS from CDNs (jsDelivr, unpkg, cdnjs) to "inspect" contents
- Using `curl`, `wget`, or the `webfetch` tool to read script source code
- Piping downloaded scripts into `node`, `python`, `bash`, `deno`, or any interpreter
- Extracting tarballs/zip/npm packages just to look at contents
- Downloading `.tgz`, `.zip`, `.tar.gz` from package registries
- Reading minified/bundled JS "to see what's inside"

### Why:
1. **Security**: Downloaded JS is executable code and an exploitation vector.
2. **Copyright**: npm packages often bundle copyrighted material. A community dump doesn't grant legal rights.
3. **Prompt injection**: JS files can contain obfuscated payloads designed to manipulate the assistant.
4. **Precedent**: You can't know a file is "safe" before downloading it.

### Allowed:
- Official docs (READMEs, API docs, `.d.ts` type definitions)
- Package metadata (npm registry JSON, GitHub API repo listing, directory structure)
- Web searches for library information
- Ask the user directly

### Enforcement:
- Before any HTTP fetch: ask "Is this executable/script content?" If yes → STOP.
- TypeScript types (`.d.ts`) are always sufficient for understanding an API.
- This applies to ALL internet sources: GitHub raw, CDNs, APIs, everything.

On 2026-04-09, an assistant downloaded `biblia-de-jerusalen` npm `.tgz` and fetched bundled minified JS from jsDelivr to "inspect" a copyrighted Catholic Bible translation. Correct action: check `.d.ts` types and npm metadata only.

## Python Package Installation

NEVER use `pip install --break-system-packages`. Prefer clean approaches:

1. `uv pip install .` (if uv is available)
2. Create a venv first: `python -m venv .venv && source .venv/bin/activate && pip install .`
3. `pipx install .` for CLI tools

Only use `--break-system-packages` if the user explicitly requests it.

## Code Quality

- Run builds/tests before any commit consideration.
- Write tests for every new feature — 100% coverage: positive, negative, edge cases, integration.
- Add fuzzing for projects handling user input, files, or network.
- Rust: run `cargo clippy -- -D warnings` and `cargo fmt --check` before commits. Use `cargo install --path .` for install instructions.
- C/C++: run `cppcheck --enable=all --error-exitcode=1 .` before commits.
- Monitor CI with `gh run watch`/`gh run list` after pushing. Fix breakages immediately.
- Use `euxaristia` in instructions, not placeholder usernames.

## Testing Policy

ALWAYS write comprehensive tests (aim for 100% coverage) for every feature, module, and codepath.

- Every function, branch, and error path must have a corresponding test.
- Tests must pass before committing. Run the test suite (`cargo test`, `pytest`, etc.) and fix any failures before creating a commit.
- If you modify existing code, update or add tests to cover the changes.
- Never skip writing tests because the code is "simple" or "unlikely to break."
- A "100% coverage" claim with failing tests is worthless. Always run tests and confirm zero failures before declaring done.