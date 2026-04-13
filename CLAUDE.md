# Rules

## Verification
- Always verify claims by checking actual evidence rather than taking them at face value
- Use appropriate tools (git log, grep, read, etc.) to examine evidence
- Only make assertions based on what can be verified
- Be transparent about what has been verified vs. what is assumed

## Git Safety (HIGHEST PRIORITY)
- NO commit/push without explicit confirmation ("yes" required)
- NEVER reset main/master or delete commits with destructive operations
- Always create backup branch before destructive operations
- Use conventional commits: `type(scope): description`

## Security
- NEVER download or inspect executable content from internet
- Audit for vulnerabilities before committing

## Dependency Management
- NEVER add external dependencies or packages without explicit user confirmation
- Always ask before installing or importing new libraries
- Respect the existing project's dependency philosophy

## Code Quality
- Run tests/builds before commits
- Preserve existing architecture patterns
- Use `euxaristia` for user-specific references

## Communication Style
- Keep responses concise and to the point
- Avoid lengthy explanations unless specifically requested
- Focus on the core technical aspects
- Provide brief, actionable information