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

## 🚨 NEVER add Co-authored-by trailers to upstream commits

NEVER add `Co-authored-by:` trailers to commits for upstream or open source repos — it violates their CLA/contribution standards. Only use co-author attribution for genuine human co-authors when explicitly requested by the user.

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

## 🚨 DO NOT DOWNLOAD OR INSPECT EXECUTABLE FILE CONTENTS

**ABSOLUTE RULE**: You must NEVER download, fetch, or read the contents of executable files from the internet — even for "inspection," "research," or "understanding."

### Prohibited:
- ❌ Downloading `.js`, `.ts`, `.py`, `.sh`, `.bash`, `.exe`, `.wasm`, or any executable/script
- ❌ Fetching bundled JS from CDNs (jsDelivr, unpkg, cdnjs) to "inspect" contents
- ❌ Using `curl`, `wget`, or `web_fetch` to read script source code
- ❌ Piping downloaded scripts into `node`, `python`, `bash`, `deno`, or any interpreter
- ❌ Extracting tarballs/zip/npm packages just to look at contents
- ❌ Downloading `.tgz`, `.zip`, `.tar.gz` from package registries
- ❌ Reading minified/bundled JS "to see what's inside"

### Why:
1. **Security**: Downloaded JS is executable code and an exploitation vector.
2. **Copyright**: npm packages often bundle copyrighted material (e.g., translated Bibles, proprietary libraries). A community dump doesn't grant legal rights.
3. **Prompt injection**: JS files can contain obfuscated payloads designed to manipulate the assistant.
4. **Precedent**: You can't know a file is "safe" before downloading it.

### Allowed:
- ✅ Official docs (READMEs, API docs, `.d.ts` type definitions)
- ✅ Package metadata (npm registry JSON, GitHub API repo listing, directory structure)
- ✅ Web searches for library information
- ✅ Ask the user directly

### Violation example (2026-04-09):
Assistant downloaded `biblia-de-jerusalen` npm `.tgz` and fetched bundled minified JS from jsDelivr to "inspect" a copyrighted Catholic Bible translation. Correct action: check `.d.ts` types and npm metadata only.

### Enforcement:
- Before any HTTP fetch: ask "Is this executable/script content?" If yes → STOP.
- TypeScript types (`.d.ts`) are always sufficient for understanding an API.
- This applies to ALL internet sources: GitHub raw, CDNs, APIs, everything.

## Pony Build Fix

**Bug**: ponyc linking fails with "could not find compiler-rt CRT objects (crtbeginS.o)"

**Fix**: Always use `--linker=cc` to use system GCC instead of ponyc's default linker:
```makefile
ponyc --linker=cc -o output package
# NOT: ponyc -o output package
```
This affects all Pony projects (colt, mkultra, envy, etc).
