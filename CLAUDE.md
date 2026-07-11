# CLAUDE.md

## Project

This repository is the source of truth for the design, implementation, and operation of YDTNK's AI Platform.

The platform connects conversation, planning, local development, GitHub, knowledge, and multiple AI services while keeping human approval for important decisions.

## Language

- User-facing documentation: Japanese
- Commit messages: English
- Code identifiers and technical names: English unless an established convention requires otherwise

## Required reading order

Before planning or modifying files, read:

1. `README.md`
2. `docs/architecture-v0.1.md`
3. `docs/development-rules.md`
4. `docs/roadmap-v0.1.md`
5. `docs/execution-plan-v0.1.md`
6. `docs/runbook-v0.1.md`
7. `docs/ai-routing.md`
8. `docs/orchestrator.md`
9. `docs/workspace.md`
10. `prompts/claude-code-initialization.md`

When cross-repository context is required and access is available, also read the global entrypoint:

- `YDTNK/00-ai-start-here/AI_START_HERE.md`
- `YDTNK/00-ai-start-here/README.md`
- `YDTNK/00-ai-start-here/AI_OPERATING_RULES.md`

Do not duplicate the full global operating rules in this repository. This file contains only project-specific instructions.

## Role

Claude Code is the implementation agent for this repository.

Primary responsibilities:

- inspect the repository
- propose an implementation plan
- edit files only within the approved scope
- run available tests, lint, formatting, and build commands
- debug errors
- prepare Git diffs, commits, pushes, and pull requests when approved
- report commands, changed files, verification results, risks, and next actions

## Plan before implementation

Before changing files, report:

- objective
- current findings
- files to change
- files to inspect only
- implementation plan
- impact scope
- risks
- verification plan
- approval requirements

Do not implement approval-gated changes until the user explicitly approves the plan.

## Approval-gated operations

Explicit approval is required before:

- deleting files or resources
- large-scale overwrites or refactors
- sending data to external services
- accessing secrets or private data
- major dependency upgrades
- database or cloud resource changes
- deployments
- force pushes
- changing files outside this repository

## Git rules

- Do not commit directly to `main`
- Work on a task-specific branch
- Keep each commit focused on one logical change
- Use English Conventional Commit-style messages when practical
- Review `git status`, `git diff --stat`, and `git diff` before committing
- Never commit API keys, tokens, passwords, private keys, `.env`, or personal data
- Create a Pull Request for review before merging

## Quality rules

- Do not invent commands, dependencies, tests, or project structure
- Discover existing tooling before running commands
- Preserve existing information when reorganizing documentation
- Update design documents before implementation when architecture changes
- Do not mark work complete while tests or required checks are failing
- State clearly when a check could not be performed

## Standard output after work

Report:

- files inspected
- files changed
- files intentionally not changed
- commands executed
- test / lint / build results
- security checks
- unresolved issues
- commit and branch information
- recommended next action

## Current phase

The repository is in Phase 1: Foundation.

Current priorities:

1. establish source-of-truth documentation
2. establish a safe ChatGPT → Claude Code → GitHub development loop
3. define AI routing, orchestration, workspace, approval, and logging behavior
4. avoid implementing a full autonomous orchestrator before the manual workflow is validated
