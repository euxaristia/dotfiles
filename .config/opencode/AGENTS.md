# Agent instructions

## Critical: No Package Management Without Explicit Approval (HIGHEST PRIORITY)

NEVER install, remove, or update system packages (npm, apt, pip, etc.) or modify package manager state (repos, sources, registries) without first asking the user and getting explicit approval.

NEVER run `npm install`, `npm i -g`, `pip install`, `apt install`, or any command that mutates globally-installed packages or system state without asking first.

If a build has unmet dependencies, report what's missing and ask the user how they want to proceed — do not silently install things.

NEVER run package manager commands like `install`, `update`, `remove`, `add`, or `i` whether via npm, yarn, bun, pip, apt, or any other manager, without asking the user first.

## PR workflow

- When a PR has review comments, push fixes to the PR branch — do NOT create a separate branch/PR for the fixes.
- The gemini-code-assist review bot only reviews when the PR is first opened — it does NOT re-review after force-pushes.
- After pushing fixes, if the bot already reviewed and the issues are addressed, it's safe to merge without waiting for re-review.
- Never merge a PR you just created — it needs review first.
- Never bypass branch protection by creating a new PR to merge into main behind the bot's back.

## Branch Protection

The `main` branch is protected on GitHub (required PR reviews, enforce admins, no deletions/force pushes). If you temporarily remove protection (e.g. to delete the branch during rename), you **must reapply it immediately** using:

```bash
gh api repos/euxaristia/pcc/branches/main/protection -X PUT --input - <<'EOF'
{
  "required_status_checks": null,
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": false,
    "required_approving_review_count": 0
  },
  "restrictions": null
}
EOF
```
