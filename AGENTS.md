## PR workflow

### Before creating a PR

- Run lint/typecheck/build before committing — verify the project compiles cleanly.
- Review `git status`, `git diff`, and recent commits to confirm only intended changes are included.

### PR standards

Every PR title and body must follow these rules:

**Title format:** `<type>: <short imperative description>`

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `style`, `perf`, `test`.

- Capitalize the description.
- No trailing period.
- Keep under 72 characters.

**Body format:**

```
<why — what problem does this solve?>

<how — what changed and key design decisions>

<testing — how was this verified?>
```

Write in complete sentences. Cover what and why, not just how. If the change is user-facing, note the impact. Reference related issues with `#N`.

**Example:**

```
feat: Add flatpak update support alongside apt updates

Running adapt without arguments (or with -Syu) previously only handled apt
packages. Flatpak is a common runtime on Linux and users expect their
Flatpak applications to be updated alongside system packages.

Add a new flatpak module that checks for the flatpak CLI at runtime. If
present, it runs flatpak update after the apt upgrade completes. If flatpak
is not installed, the step is silently skipped so there is no error noise
on systems without it.

Verified by building with cargo build and running adapt on a system with
and without flatpak installed.
```

### Review & merge

- When a PR has review comments, push fixes to the PR branch — do NOT create a separate branch/PR for the fixes.
- The `gemini-code-assist` review bot only reviews when the PR is first opened — it does NOT re-review after force-pushes.
- After pushing fixes, if the bot already reviewed and the issues are addressed, it's safe to merge without waiting for re-review.
- Never merge a PR you just created — it needs review first.
- Never bypass branch protection by creating a new PR to merge into main behind the bot's back.
