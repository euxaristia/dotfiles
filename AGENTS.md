## PR workflow

### Before creating a PR

- Run lint/typecheck/build before committing — verify the project compiles cleanly.
- Review `git status`, `git diff`, and recent commits to confirm only intended changes are included.

### PR standards

Every PR must follow this structure exactly, matching the Claude Code style.

**Title format:** `type(scope): short imperative description`

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `style`, `perf`, `test`.

Scope is the subsystem or module (e.g. `cli`, `core`, `api`, `flatpak`).

- Capitalize the description. No trailing period. Keep under 72 chars.

**Body format — every section is required:**

```
## Summary

<one paragraph explaining what problem this solves and the approach taken.
Focus on why — the user impact and the design intent.>

## Changes

**`path/to/file.rs`**

-   <what changed here and why. One bullet per logical change in this file.>

**`path/to/other.rs`**

-   <bullet explaining the change and its rationale.>

<Organize by file. If a change spans multiple files, group related changes under one file heading.>

## Test plan

-    `<command>` — <result (e.g. "17/17 pass", "`tsc --noEmit` clean")>
-    Manual: <specific scenario tested and expected behavior>
```

- Reference related issues: `Fixes #N` or `Refs #N` at the top of the body.
- Every commit message must be a complete sentence in imperative mood with a detailed body explaining **why** the change was made, not just what changed. Use `Refs #N` in commit footers.
- If addressing prior review feedback, add a `## Prior reviewer feedback addressed` section with a checklist:
  ```
  -   ✅ <issue 1> ([link to review](#discussion_rN))
  -   ✅ <issue 2> ([link to review](#discussion_rN))
  ```
- Each round of review feedback should be addressed in separate fixup commits (not squashed) so reviewers can see incremental changes.

**Example:**

```
feat(flatpak): add flatpak update support alongside apt updates

Refs #2

## Summary

Running adapt without arguments (or with -Syu) previously only handled
apt packages. Flatpak is a common runtime on Linux and users expect
their Flatpak applications to be updated alongside system packages.

Add a new flatpak module that checks for the flatpak CLI at runtime. If
present, it runs `flatpak update` after the apt upgrade completes. If
flatpak is not installed, the step is silently skipped so there is no
error noise on systems without it.

## Changes

**`src/flatpak.rs`**

-   Check for `flatpak` on PATH via `command -v flatpak`; return
    `Ok(())` silently if not found — no error output on systems
    without flatpak.
-   Run `flatpak update` with inherited stdio so the user sees
    real-time progress. Pass `-y` when `noconfirm` is set.

**`src/main.rs`**

-   Call `flatpak::update()` after both apt paths that trigger
    update+upgrade: the default no-op path and the `-Syu` flags path.

## Test plan

-    `cargo build` — compiles cleanly
-    Run `adapt` on a system without flatpak: silently skipped, exits 0
-    Run `adapt` on a system with flatpak installed: updates run after apt
```

### Review & merge

- When a PR has review comments, push fixes to the PR branch — do NOT create a separate branch/PR for the fixes.
- The `gemini-code-assist` review bot only reviews when the PR is first opened — it does NOT re-review after force-pushes.
- After pushing fixes, if the bot already reviewed and the issues are addressed, it's safe to merge without waiting for re-review.
- Never merge a PR you just created — it needs review first.
- Never bypass branch protection by creating a new PR to merge into main behind the bot's back.
