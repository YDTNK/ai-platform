# ADR-0001: AI Platform Core技術スタック

Status: ACCEPTED
Date: 2026-07-11

## Context

AI PlatformはChatGPT、Claude Code、Gemini、MCP、GitHub、Local Toolを統合しながら、人間承認、状態復元、Provider差し替え、実行追跡を維持する必要がある。

## Decision

### Runtime

- Python 3.12
- uv
- pyproject.toml
- uv.lock

### Application

- FastAPI: HTTP API
- Typer: Local CLI
- Pydantic: Schema・Config Validation
- SQLAlchemy 2.0: Persistence
- Alembic: Schema Migration
- SQLite: Phase 3〜6 Local Database
- PostgreSQL: Phase 7 Production Database

### Core Architecture

~~~text
interface
  ├─ cli
  └─ api
application
  ├─ commands
  ├─ queries
  └─ services
domain
  ├─ task
  ├─ execution
  ├─ approval
  ├─ decision
  └─ state_machine
infrastructure
  ├─ persistence
  ├─ providers
  ├─ mcp
  ├─ github
  └─ telemetry
~~~

Domain層はProvider SDK、FastAPI、SQLAlchemyへ依存しない。

### Provider Adapter

~~~text
AgentProvider Protocol
├─ OpenAIProvider
├─ ClaudeProvider
├─ GeminiProvider
└─ FakeProvider
~~~

最初にFakeProviderでState・Approval・RetryをTestし、外部APIは後から追加する。

### State

~~~text
DRAFT
READY
PLANNING
WAITING_APPROVAL
RUNNING
VERIFYING
COMPLETED
BLOCKED
FAILED
CANCELLED
~~~

State TransitionはDomain Serviceだけが変更できる。Provider出力から直接Statusを書き換えない。

### Quality

- pytest
- pytest-asyncio
- Ruff Linter・Formatter
- mypy strictを段階導入
- coverage
- OpenTelemetry
- GitHub Actions

## Consequences

### Positive

- AI Providerを差し替えやすい
- ApprovalとStateをLLMから独立して強制できる
- Local Firstで費用を抑えられる
- Type、Migration、Test、Traceを初期から維持できる

### Negative

- SDKを直接使う場合よりAdapter実装が増える
- SQLiteとPostgreSQLの差異をTestする必要がある
- Voice・Knowledge UIの実装開始は遅くなる

## Rejected alternatives

### LangGraphをCoreにする

Graph実装には有力だが、初期からCore StateとPersistenceをFrameworkへ固定するため不採用。必要な場合はPhase 4でAdapterまたはWorkflow実装として再評価する。

### OpenAI Agents SDKのみで統一する

OpenAI機能には適するが、Claude・Gemini差し替えとPlatform固有承認を維持するためCoreにはしない。

### Google ADKのみで統一する

Multi-Agent Workflowに適するが、Provider中立Coreを維持するためGemini Adapterに限定する。

### Node.jsをCoreにする

MCP・Frontendとの相性は良いが、AI SDK・Data処理・UserのPython学習資産を考慮してCoreはPythonとする。Frontendが必要なPhaseではTypeScriptを追加できる。
