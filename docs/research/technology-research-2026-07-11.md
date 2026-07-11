# AI Platform技術調査 2026-07-11

## 1. 目的

AI PlatformのPhase設計と技術選定を、公式資料・一次情報に基づいて再評価する。

## 2. 調査結論

- Core状態・承認・履歴は特定Agent SDKへ委譲せず、AI PlatformのDomain層で管理する
- Agent SDKはProvider Adapterとして利用し、差し替え可能にする
- Tool・Data Source接続はMCPを標準境界とする
- 最初から完全自動化せず、手動開発ループ → Core → Orchestrationの順で進める
- 実行可能な固定フローはコードで制御し、LLMには曖昧さの処理だけを任せる
- ローカルSQLiteで開始し、Schema Migrationを維持してPostgreSQLへ移行可能にする
- 全Phaseで再現可能なLock、Static Check、Test、Trace、CIを維持する

## 3. 主要な根拠

### MCP

MCPはLLM ApplicationとTool・Data Sourceを接続するOpen Protocolである。実装基準はstable specificationを利用し、draftだけへ依存しない。

- Specification 2025-11-25: https://modelcontextprotocol.io/specification/2025-11-25
- Authorization: https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- Claude Code MCP: https://docs.anthropic.com/en/docs/claude-code/mcp

判断:

- Phase 4でMCP Clientを導入
- 独自Tool APIを増やす前にMCPで表現可能か確認
- Remote HTTP MCPはAuthorization、Token保存、最小権限を必須化
- MCP draftの破壊的変更を避けるためVersionを明示する

### OpenAI Agents SDK

Agents SDKはTool実行、Guardrail、Handoff、Session、Tracingを提供するが、HandoffとTool Guardrailの適用境界は異なる。

- Overview: https://openai.github.io/openai-agents-python/
- Guardrails: https://openai.github.io/openai-agents-python/guardrails/
- Handoffs: https://openai.github.io/openai-agents-python/handoffs/
- Tracing: https://openai.github.io/openai-agents-python/tracing/
- Running agents: https://openai.github.io/openai-agents-python/running_agents/

判断:

- OpenAI固有のAgent LoopはAdapter内へ閉じ込める
- ApprovalはSDK Guardrailだけに依存せずCore状態機械で強制する
- Provider TraceとPlatform共通Trace IDを関連付ける

### Claude Agent SDK・Claude Code

Claude Agent SDKはClaude Codeと同系統のAgent Loop、File、Command、Context ManagementをProgrammaticに利用できる。Claude CodeはMCP、Hooks、GitHub Actionsにも対応する。

- Agent SDK: https://docs.anthropic.com/en/docs/claude-code/sdk
- Hooks: https://docs.anthropic.com/en/docs/claude-code/hooks
- Settings: https://docs.anthropic.com/en/docs/claude-code/settings
- GitHub Actions: https://docs.anthropic.com/en/docs/claude-code/github-actions

判断:

- Phase 2まではClaude Code CLIで手動検証
- Phase 4でClaude Agent SDK Adapterを追加
- HooksはSecurity CheckやAudit補助に使い、Core承認の代替にしない
- Project共有設定とMachine固有設定を分離する

### Google ADK

Google ADKは単一AgentからMulti-Agent、Graph Workflow、Evaluation、Deploymentへ拡張でき、固定順序のSequential Workflowも提供する。

- Overview: https://google.github.io/adk-docs/
- Multi-agent: https://google.github.io/adk-docs/agents/multi-agents/
- Workflow agents: https://google.github.io/adk-docs/agents/workflow-agents/
- Sequential agents: https://google.github.io/adk-docs/agents/workflow-agents/sequential-agents/

判断:

- Gemini統合はPhase 4のProvider Adapterとして実施
- 固定順序はDeterministic Workflow、曖昧なRoutingだけLLMへ任せる
- ADKをPlatform全体の唯一のOrchestratorにはしない

### Python・依存関係管理

uvはpyproject.toml、Lockfile、Sync、Runを一体管理し、uv run時にEnvironmentとLockの整合を確認する。

- Project guide: https://docs.astral.sh/uv/guides/projects/
- Lock and sync: https://docs.astral.sh/uv/concepts/projects/sync/
- Dependencies: https://docs.astral.sh/uv/concepts/projects/dependencies/

判断:

- Python 3.12
- pyproject.toml + uv.lockを正本にする
- CIではuv sync --lockedとuv runを使用する

### API・Data Validation

FastAPIはPython Type HintとDependency Injectionを基盤にし、Test時のDependency Overrideを提供する。Pydanticで境界DataをValidationする。

- FastAPI: https://fastapi.tiangolo.com/
- Dependencies: https://fastapi.tiangolo.com/tutorial/dependencies/
- Testing overrides: https://fastapi.tiangolo.com/advanced/testing-dependencies/
- Pydantic: https://docs.pydantic.dev/latest/
- Pydantic Settings: https://docs.pydantic.dev/latest/concepts/pydantic_settings/

判断:

- HTTP APIはFastAPI
- Request、Response、Config、Provider入出力はPydantic Model
- SecretはEnvironmentまたはSecret Storeから読み、LogやRepositoryへ保存しない

### Database・Migration

SQLAlchemy 2.0とAlembicはORMとSchema Migrationを分離管理できる。Alembic autogenerateは候補を生成するため、必ずReviewする。

- SQLAlchemy 2.0: https://docs.sqlalchemy.org/en/20/
- Alembic tutorial: https://alembic.sqlalchemy.org/en/latest/tutorial.html
- Autogenerate: https://alembic.sqlalchemy.org/en/latest/autogenerate.html

判断:

- Phase 3はSQLiteでLocal First
- Alembic Revisionを必ずCommit
- Phase 7でPostgreSQLへ移行
- Migration生成後にUpgrade・Downgrade・Fresh DBをTestする

### Quality・Observability・CI

- pytest: https://docs.pytest.org/en/stable/
- Ruff Linter: https://docs.astral.sh/ruff/linter/
- Ruff Formatter: https://docs.astral.sh/ruff/formatter/
- mypy: https://mypy.readthedocs.io/
- OpenTelemetry Python: https://opentelemetry.io/docs/languages/python/instrumentation/
- GitHub Actions: https://docs.github.com/actions
- Secure use: https://docs.github.com/en/actions/reference/security/secure-use
- OIDC: https://docs.github.com/en/actions/concepts/security/openid-connect

判断:

- Unit・Integration・Contract・E2Eを分離
- ruff check、ruff format --check、mypy、pytestをCI Gateにする
- Trace ID、Task ID、Execution IDをLog・Spanへ付与する
- Workflow Permissionを最小化する
- Cloud認証は長期SecretよりOIDCを優先する

## 4. 採用しない設計

- 1つのAgent SDKをPlatform Coreそのものにする
- LLMに状態遷移・承認・Retry上限を全面委任する
- Phase 1からMicroservice化する
- Schema MigrationなしでSQLiteから開始する
- API KeyをGitHub、設定ファイル、Logへ保存する
- Phase 3完成前にVoice・Vector Search・Multi-userへ進む
- Draft MCP仕様へ無条件追従する

## 5. 再評価条件

以下が起きた場合、ADRとPhase手順を再評価する。

- Stable MCP Specificationの互換性に影響する更新
- Provider SDKのBreaking Change
- SQLiteではConcurrencyまたはData量要件を満たせない
- Local CLIだけでは実行継続性を満たせない
- Phase完了条件が実測で不十分と判明
