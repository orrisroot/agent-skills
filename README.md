# Agent Skills

This repository manages Agent Skills.

## Structure

```text
skills/<skill-name>/SKILL.md
```

Each skill lives in its own directory.
It carries its own instructions and optional bundled resources.

## Included skills

- `git-commit`: analyzes staged changes, gathers contextual intent, and generates structurally compliant commit messages with executable terminal commands.
- `review-debate`: facilitates a multi-perspective code review debate between Security/Performance and Clean Architecture/Readability experts, synthesized by a Tech Lead.
- `architecture-refactor`: orchestrates an architectural review, security boundary assessment, and incremental refactoring plan using specialized expert perspectives.
- `update-docs`: Analyzes the codebase state, updates project documentation to reflect the latest status, and manages release-based changelogs based on Git tags (omitting changelogs for unreleased code).

