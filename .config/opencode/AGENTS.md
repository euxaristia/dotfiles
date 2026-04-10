# Global Rules

## Git Commit Messages

ALWAYS use Conventional Commits format for all commit messages:

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
- `chore`: Changes to build process, auxiliary tools, or dependencies

Rules:
- Use imperative mood in description ("add feature" not "added feature")
- Keep subject line under 50 characters
- Do not end subject line with a period
- Separate subject from body with a blank line
- Wrap body at 72 characters

## Python Package Installation

NEVER use `pip install --break-system-packages`. Prefer clean approaches:

1. `uv pip install .` (if uv is available)
2. Create a venv first: `python -m venv .venv && source .venv/bin/activate && pip install .`
3. `pipx install .` for CLI tools

Only use `--break-system-packages` if the user explicitly requests it.
