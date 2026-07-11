# CLAUDE.md

## Project

This repository is the source of truth for the design and implementation of YDTNK's AI Platform.

The platform connects conversation, planning, local development, GitHub, knowledge, and multiple AI services while keeping human approval for important decisions.

Project progress, daily-log, fragments, and review tasks are managed in:

```text
YDTNK/engineering-career-hq/projects/ai-platform/
```

## Language

GitHub全体の言語・命名ポリシーは、以下を正本とする。

~~~text
YDTNK/00-ai-start-here/docs/language-policy.md
~~~

本Repositoryでは次を適用する。

- README、設計書、Runbook、Issue本文、Pull Request本文など、人間が読み返す文章は日本語
- Branch名、Commit message、Issue・Pull Requestタイトル、ファイル名、コード識別子、設定キー、固定状態値は英語
- 技術名称、コマンド、API、規格の固有表記は原文を維持
- 既存英語文書は実質的な更新機会に日本語へ整え、一括翻訳や情報消失は行わない
- 共通ポリシー全文をこのRepositoryへ複製しない

## Required reading order

Before planning or modifying files, read:

1. `README.md`
2. `docs/requirements-v0.1.md`
3. `docs/architecture-v0.1.md`
4. `docs/roadmap-v0.1.md`
5. `docs/execution-plan-v0.1.md`
6. `docs/runbook-v0.1.md`
7. `docs/ai-routing.md`
8. `docs/orchestrator.md`
9. `docs/workspace.md`
10. `docs/development-rules.md`
11. `prompts/claude-code-initialization.md`

When continuing prior work and cross-repository access is available, also read:

- `YDTNK/00-ai-start-here/AI_START_HERE.md`
- `YDTNK/00-ai-start-here/README.md`
- `YDTNK/00-ai-start-here/AI_OPERATING_RULES.md`
- `YDTNK/engineering-career-hq/projects/ai-platform/START_HERE_FOR_AI.md`
- `YDTNK/engineering-career-hq/projects/ai-platform/project-context.md`
- `YDTNK/engineering-career-hq/projects/ai-platform/progress.md`
- the latest relevant daily-log, fragment, and review task
- the relevant Issue, Pull Request, commit, and design documents

Do not duplicate the full global operating rules or project-management records in this repository.

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
- provide the information needed to update the management-side daily-log and progress records

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

## Record and synchronization rules

- Keep architecture and implementation documentation in this repository
- Keep implementation facts in code, tests, Issues, Pull Requests, and Commits
- Keep project status, daily-log, fragments, and review tasks in `YDTNK/engineering-career-hq/projects/ai-platform/`
- Do not create a parallel `reports/` daily-report structure in this repository during the current foundation phase
- Never claim tests, pushes, merges, or external actions were completed when they were not verified
- Preserve information before moving or deleting files

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
- management-side progress and daily-log updates required
- cross-repository sync decision
- recommended next action

## Current phase

The repository is in Phase 1: Foundation.

Current priorities:

1. establish source-of-truth documentation
2. establish a safe ChatGPT -> Claude Code -> GitHub development loop
3. define AI routing, orchestration, workspace, approval, and logging behavior
4. validate the manual cross-repository workflow before automating it
5. avoid implementing a full autonomous orchestrator before the manual workflow is validated
