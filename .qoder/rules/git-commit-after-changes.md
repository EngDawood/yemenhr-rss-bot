---
trigger: model_decision
description: - Commit immediately after making any significant code changes - Commit after completing each logical unit of work (e.g., implementing a feature, fixing a bug) - Commit before switching to a different task or taking a break - Commit after successfully testing new functionality
---
## Brief overview
This rule establishes a workflow guideline for automatically committing changes to git repositories after making modifications. This ensures proper version control and maintains a clear history of development progress.

## Commit best practices
- Write clear, descriptive commit messages that explain what changed and why
- Keep commits focused on a single purpose
- Use present tense in commit messages (e.g., "Add user authentication feature" not "Added user authentication feature")
- Include issue numbers or references when relevant to project tracking

## Git workflow
- Always check the status of files before committing using `git status`
- Stage only the files that are part of the current logical change
- Verify commits with `git log` or `git show` when needed
- Push commits regularly to remote repositories to maintain backup and collaboration

## Automation considerations
- When working in ACT MODE, automatically execute git commit commands after file modifications
- When working in PLAN MODE, plan for git commits as part of the implementation strategy
- Include commit messages as part of the documentation for each change made
