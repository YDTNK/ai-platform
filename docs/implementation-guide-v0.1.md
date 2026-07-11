# AI Platform実装手順書 v0.1

## 1. この手順書の使い方

本書はPhase 1からPhase 7までを、原則として上から順に実行するためのMaster Playbookである。

各Phaseで必ず次を守る。

1. 前PhaseのCompletion Gateを確認
2. GitHub Issueを作成または選択
3. mainを更新
4. Task Branchを作成
5. Claude Codeへ変更なしの事前調査を依頼
6. 人間が計画を承認
7. 本書のコマンドを対象環境で確認して実行
8. Test・Security・Diffを確認
9. Pull Request
10. Review・Merge
11. engineering-career-hqを同期
12. Completion Gateを判定

コマンドはRepository rootで実行する。Commandが失敗した場合は次へ進まず、Error・Environment・Version・Diffを保存する。

## 2. 共通前提

~~~bash
git --version
gh --version
claude --version
python3 --version
uv --version
~~~

不足時:

~~~bash
brew install git gh uv
gh auth login
~~~

Claude CodeはAnthropic公式手順で導入済みであることを確認する。

共通Git開始手順:

~~~bash
git switch main
git pull --ff-only origin main
git status --short
git switch -c <english-task-branch>
~~~

共通終了確認:

~~~bash
git status --short
git diff --stat
git diff
git diff --check
~~~

## 3. Phase 1: Foundation

### 3.1 Goal

要件、設計、完成経路、実行手順、安全基盤、GitHub Template、Claude Code Ruleを揃え、実装を安全に開始できる状態にする。

### 3.2 必要ファイル確認

~~~bash
required_files=(
  README.md
  CLAUDE.md
  .gitignore
  docs/requirements-v0.1.md
  docs/architecture-v0.1.md
  docs/development-rules.md
  docs/roadmap-v0.1.md
  docs/execution-plan-v0.1.md
  docs/runbook-v0.1.md
  docs/ai-routing.md
  docs/orchestrator.md
  docs/workspace.md
  docs/implementation-guide-v0.1.md
  docs/adr/0001-core-technology-stack.md
  prompts/claude-code-initialization.md
  .github/ISSUE_TEMPLATE/feature.yml
  .github/pull_request_template.md
)

for file in "${required_files[@]}"; do
  test -f "$file" || echo "MISSING: $file"
done
~~~

MISSINGが1件でもあればPhase 1を完了しない。

### 3.3 古い参照確認

~~~bash
rg -n "reports/|execution-plan-v0.1.md.*作成|runbook-v0.1.md.*作成" .
rg -n "requirements-v0.1.md" README.md CLAUDE.md docs prompts
~~~

### 3.4 秘密情報確認

~~~bash
find . -type f \( -name ".env" -o -name "*.pem" -o -name "*.key" -o -name "*.p12" \) -print
git grep -n -I -E "(api[_-]?key|secret|token|password)[[:space:]]*[:=][[:space:]]*[^<${]" -- . || true
~~~

検出結果は値を表示・共有せず、追跡対象かどうかだけを確認する。

### 3.5 Claude Code初回調査

~~~bash
claude
~~~

入力:

~~~text
prompts/claude-code-initialization.mdとCLAUDE.mdを読み、必読文書を順番に確認してください。
この段階ではファイル変更、Package導入、Commit、Pushを行わないでください。
RequirementsからIssueまでの追跡性、矛盾、不足、Risk、Phase 1完了可否を報告してください。
~~~

Claude終了後:

~~~bash
git status --short
~~~

意図しない変更があれば停止する。

### 3.6 GitHub管理構造

- Epic Issue: Phase 1全体
- Milestone: Phase 1 — Foundation
- Issue: FND-001〜FND-004
- Task: Issue本文のChecklist
- Phase 2 Issue: #1を再利用

### 3.7 Completion Gate

- 必要ファイルが全て存在
- Requirements・Architecture・Roadmap・Execution Planに矛盾なし
- Claude Codeが目的・FR・NFR・承認条件を説明
- 秘密情報なし
- Epic・Milestone・Issue・Task登録済み
- 管理側progress更新済み

## 4. Phase 2: Local Development Loop

### 4.1 Goal

Issue #1を使い、ChatGPT → Claude Code → GitHub → engineering-career-hqを1回完走する。

### 4.2 Local同期

~~~bash
git switch main
git pull --ff-only origin main
git status
git log -1 --oneline
~~~

### 4.3 Claude Code事前調査

~~~bash
claude
~~~

入力:

~~~text
GitHub Issue #1、CLAUDE.md、Requirements、Architecture、Roadmap、Execution Plan、Runbookを確認してください。
まだ変更しないでください。
目的、対応Requirement、変更対象、対象外、実装手順、Risk、Test、Rollback、完了条件を提示してください。
~~~

人間承認後:

~~~bash
git switch -c feature/local-development-loop
claude
~~~

入力:

~~~text
承認した範囲だけを実装してください。
実装後に実在するTest・Lint・Build、git status、git diff、秘密情報確認を行い、結果を報告してください。
Commit・Pushはまだ行わないでください。
~~~

### 4.4 Review・Commit

~~~bash
git status --short
git diff --stat
git diff
git diff --check
git add <approved-files>
git diff --cached --stat
git diff --cached
git commit -m "feat: establish the local development loop"
git push -u origin HEAD
gh pr create --draft --fill
~~~

### 4.5 Completion Gate

- 承認前変更なし
- Feature Branch使用
- Test成功または対象外理由あり
- PR Review・Merge済み
- Issue #1 Completion Criteria達成
- fragment・daily-log・progress同期済み

## 5. Phase 3: AI Platform Core

### 5.1 Goal

Providerに依存しないTask、State、Approval、Execution、Decision、ErrorのCoreを構築する。

### 5.2 Project初期化

~~~bash
git switch main
git pull --ff-only origin main
git switch -c feature/bootstrap-platform-core

uv init --lib --python 3.12
uv add fastapi "uvicorn[standard]" typer pydantic pydantic-settings sqlalchemy alembic aiosqlite structlog
uv add --dev pytest pytest-asyncio pytest-cov httpx ruff mypy
uv lock
uv sync
~~~

uv initが既存READMEやgitignoreを上書きしようとする場合は中止し、生成差分を確認して必要ファイルだけ採用する。

### 5.3 Directory作成

~~~bash
mkdir -p src/ai_platform/{domain,application,infrastructure,interfaces/api,interfaces/cli}
mkdir -p src/ai_platform/infrastructure/{persistence,providers,telemetry}
mkdir -p tests/{unit,integration,contract,e2e}
touch src/ai_platform/__init__.py
touch src/ai_platform/domain/{__init__.py,task.py,state.py,approval.py,execution.py,errors.py}
touch src/ai_platform/application/{__init__.py,commands.py,queries.py,services.py}
touch src/ai_platform/infrastructure/persistence/{__init__.py,models.py,repositories.py,database.py}
touch src/ai_platform/infrastructure/providers/{__init__.py,base.py,fake.py}
touch src/ai_platform/interfaces/api/{__init__.py,app.py,routes.py,schemas.py}
touch src/ai_platform/interfaces/cli/{__init__.py,main.py}
~~~

### 5.4 Domain実装順

1. TaskId、ExecutionId、ApprovalIdのValue Object
2. TaskStatus Enum
3. Allowed Transition Table
4. Task Aggregate
5. Approval Entity
6. Execution Entity
7. Domain Error
8. Repository Protocol
9. FakeProvider
10. Application Service

必須遷移:

~~~text
DRAFT → READY → PLANNING → WAITING_APPROVAL
WAITING_APPROVAL → RUNNING → VERIFYING → COMPLETED
Any active state → BLOCKED | FAILED | CANCELLED
~~~

禁止遷移はDomain Errorにする。

### 5.5 Database

~~~bash
uv run alembic init migrations
~~~

初期Table:

- tasks
- task_dependencies
- approvals
- executions
- decisions
- errors
- artifacts
- events

Migration生成:

~~~bash
uv run alembic revision --autogenerate -m "create core tables"
uv run alembic upgrade head
uv run alembic current
uv run alembic downgrade base
uv run alembic upgrade head
~~~

Autogenerate結果は必ず目視Reviewする。

### 5.6 API・CLI

API:

- POST /tasks
- GET /tasks/{task_id}
- POST /tasks/{task_id}/plan
- POST /tasks/{task_id}/approve
- POST /tasks/{task_id}/run
- POST /tasks/{task_id}/cancel
- GET /tasks/{task_id}/events
- GET /health

CLI:

~~~text
ai-platform task create
ai-platform task show
ai-platform task plan
ai-platform task approve
ai-platform task run
ai-platform task cancel
ai-platform doctor
~~~

### 5.7 Test・Quality

~~~bash
uv run ruff check .
uv run ruff format --check .
uv run mypy src
uv run pytest tests/unit -q
uv run pytest tests/integration -q
uv run pytest --cov=src/ai_platform --cov-report=term-missing
~~~

最低Test:

- 全許可遷移
- 全禁止遷移
- ApprovalなしRUNNING禁止
- Retry上限
- Persistence再起動復元
- API Validation
- FakeProvider成功・失敗・Timeout
- Migration upgrade・downgrade

### 5.8 GitHub Actions

.github/workflows/ci.ymlを作成し、read-only permissionで以下を実行する。

~~~bash
uv sync --locked
uv run ruff check .
uv run ruff format --check .
uv run mypy src
uv run pytest
~~~

### 5.9 Completion Gate

- Core DomainがProvider SDK非依存
- State・Approval・RetryをUnit Test
- SQLite再起動後にTask復元
- API・CLI動作
- Migration往復成功
- CI成功

## 6. Phase 4: Orchestration

### 6.1 Goal

Provider Adapter、MCP、GitHubを接続し、Intent・Risk・Capabilityに基づく安全なOrchestrationを実装する。

### 6.2 Dependencies

~~~bash
git switch main
git pull --ff-only origin main
git switch -c feature/add-orchestration-adapters

uv add openai-agents claude-agent-sdk google-adk mcp httpx tenacity
uv lock
uv sync
~~~

導入前に各Packageの公式Install名とCurrent Stable Versionを再確認する。

### 6.3 Files

~~~bash
mkdir -p src/ai_platform/infrastructure/{mcp,github}
touch src/ai_platform/infrastructure/providers/{openai.py,claude.py,gemini.py}
touch src/ai_platform/infrastructure/mcp/{client.py,registry.py,auth.py}
touch src/ai_platform/infrastructure/github/{client.py,mappers.py}
touch src/ai_platform/application/{router.py,orchestrator.py,retry.py}
mkdir -p tests/contract/providers tests/integration/mcp
~~~

### 6.4 Adapter Contract

各Providerは以下を実装する。

- name
- capabilities
- plan
- execute
- cancel
- health
- normalize_error
- usage

Provider固有ResponseをDomainへ直接渡さず、NormalizedResultへ変換する。

### 6.5 Routing順序

1. Intent分類
2. 必要Capability抽出
3. Workspace・Permission確認
4. Risk判定
5. 利用可能Provider確認
6. Cost・Latency・Policyで選択
7. Approval Gate
8. Execute
9. Verify
10. Persist・Trace

### 6.6 MCP

最初はLocal stdioのRead-only Serverで検証する。

- tools/list
- tools/call
- resources/list
- resources/read
- Version negotiation
- Timeout
- Schema validation
- Error mapping

Remote HTTPとOAuthはLocal Test後に追加する。

### 6.7 Test

~~~bash
uv run pytest tests/contract/providers -q
uv run pytest tests/integration/mcp -q
uv run pytest tests/e2e/test_orchestration.py -q
~~~

必須Scenario:

- OpenAI成功
- Claude成功
- Gemini成功
- Provider unavailable fallback
- Tool approval required
- MCP invalid schema
- Timeout・Retry上限
- Infinite loop防止
- Cancellation
- Secret masking

### 6.8 Completion Gate

- 3 Provider Adapterが同一Contractを通る
- FakeProviderでDeterministic E2E
- MCP Read-only Tool接続成功
- 危険Tool前にWAITING_APPROVAL
- Error時に安全停止
- TraceでTaskからProvider Callまで追跡可能

## 7. Phase 5: Voice and Meeting Notes

### 7.1 Goal

音声をTextへ変換し、会話中のTranscript、終了後のSummary・Decision・TaskをWorkspaceへ保存する。

### 7.2 Dependencies・Files

~~~bash
git switch -c feature/add-voice-workflow
uv add websockets
mkdir -p src/ai_platform/infrastructure/voice
mkdir -p src/ai_platform/application/meeting
mkdir -p tests/contract/voice tests/e2e/voice
touch src/ai_platform/infrastructure/voice/{base.py,realtime.py,audio.py}
touch src/ai_platform/application/meeting/{transcript.py,extractor.py,service.py}
~~~

### 7.3 Pipeline

~~~text
Microphone
→ Audio chunks
→ Realtime Provider
→ Partial Transcript
→ Final Transcript
→ Summary
→ Decisions
→ Tasks
→ Human confirmation
→ Workspace save
~~~

### 7.4 Data Model

- meeting_sessions
- transcript_segments
- summaries
- extracted_decisions
- extracted_tasks
- consent_records

### 7.5 Test

- Recorded fixtureでTranscript Contract Test
- Network断から再接続
- Duplicate chunk防止
- PartialとFinal統合
- 保存前Human confirmation
- Audio・TranscriptのRetention
- Secret・Personal Data masking

### 7.6 Completion Gate

- LiveまたはRecorded AudioからTranscript生成
- Summary・Decision・Task抽出
- 保存先Workspace選択
- 保存前確認
- Session再開
- Privacy・Retention定義

## 8. Phase 6: Knowledge Platform

### 8.1 Goal

会話、設計、Log、Codeを出典・Workspace・Permission付きで検索する。

### 8.2 Initial implementation

SQLite FTSでKeyword Searchを先に実装し、Evaluation Datasetを作成してからVector Searchを追加する。

~~~bash
git switch -c feature/add-knowledge-search
mkdir -p src/ai_platform/domain/knowledge
mkdir -p src/ai_platform/application/search
mkdir -p src/ai_platform/infrastructure/knowledge
mkdir -p tests/evaluation/search
touch src/ai_platform/domain/knowledge/{document.py,source.py,chunk.py}
touch src/ai_platform/application/search/{indexer.py,query.py,ranking.py}
touch src/ai_platform/infrastructure/knowledge/{loaders.py,fts.py,permissions.py}
~~~

### 8.3 Pipeline

~~~text
Source
→ Permission check
→ Parse
→ Normalize
→ Chunk
→ Metadata
→ Index
→ Query
→ Rank
→ Citation
~~~

必須Metadata:

- source_uri
- repository
- workspace_id
- commit_sha
- path
- created_at
- indexed_at
- permission_scope
- content_hash

### 8.4 Evaluation

最低30件のQuestion・Expected Source Datasetを作る。

Metrics:

- Recall@5
- Citation accuracy
- Workspace leakage 0件
- Stale source detection
- Duplicate result rate

Vector SearchはKeyword Baselineを上回ることを確認して採用する。

### 8.5 Completion Gate

- Source付き検索
- Workspace境界
- Permission Test
- Incremental re-index
- Deleted・renamed source反映
- Evaluation基準達成

## 9. Phase 7: Productization

### 9.1 Goal

個人用Local Toolを複数ユーザーが安全に利用できるServiceへ移行する。

### 9.2 Infrastructure changes

- SQLite → PostgreSQL
- Local file → Object Storage
- Single process → API + Worker
- Local identity → OIDC Authentication
- Local secret → Managed Secret Store
- Local trace → OTLP Backend

### 9.3 Files

~~~bash
git switch -c feature/productize-platform
mkdir -p deploy/{docker,kubernetes,terraform}
mkdir -p docs/operations docs/security docs/privacy
touch Dockerfile
touch compose.yaml
touch docs/operations/{deployment.md,backup-restore.md,incident-response.md,upgrade.md}
touch docs/security/{threat-model.md,access-control.md,data-classification.md}
touch docs/privacy/{retention.md,deletion.md,privacy-policy-draft.md}
~~~

### 9.4 Database Migration

~~~bash
uv add asyncpg
export DATABASE_URL="postgresql+asyncpg://..."
uv run alembic upgrade head
uv run alembic current
~~~

Productionへ適用する前にBackupとRestore Testを実施する。

### 9.5 Security

- OIDC Authorization Code + PKCE
- User・Workspace・Role
- Tenant filterをRepository層で強制
- Audit Log
- Rate Limit
- CSRF・CORS・Security Headers
- Dependency・Container Scan
- Secret Rotation
- Data Export・Deletion
- GitHub Actions Cloud認証はOIDC

### 9.6 Deployment Gate

~~~bash
uv run ruff check .
uv run ruff format --check .
uv run mypy src
uv run pytest
docker build -t ai-platform:local .
docker compose up -d
docker compose ps
~~~

StagingでSmoke・Migration・Backup・Restore・Rollbackを実施してからProduction承認を求める。

### 9.7 Completion Gate

- AuthN・AuthZ・Tenant Isolation
- PostgreSQL Migration
- Backup・Restore実証
- Staging E2E
- Observability・Alert
- Incident・Upgrade・Rollback Runbook
- Privacy・Retention・Deletion
- 他ユーザーによるAcceptance Test

## 10. 全体完了判定

次を全て満たした場合のみ「AI Platform完成」とする。

- Phase 1〜7のCompletion Gateが全てCOMPLETED
- Open Critical・High Security Findingがない
- RequirementsのMUSTが全てTrace可能
- Backup・Restore・Rollbackを実証
- Provider 1つ停止時に安全停止またはFallback
- Workspace・Tenant Leakageが0件
- User SetupとAcceptance Testが成功
- 管理側progressがCOMPLETED
