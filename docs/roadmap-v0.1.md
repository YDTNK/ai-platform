# AI Platform Roadmap v0.1

## 1. 目的

本書は、AI Platformを完成させるまでのPhase、各Phaseの目的、成果物、完了条件、依存関係を定義する。

最上位要件はdocs/requirements-v0.1.mdとし、各Phaseは対応する機能要件・非機能要件を満たす。

## 2. 全体方針

- Requirements → Architecture → Roadmap → Execution Plan → Issue → Implementationの順序を維持する
- 各Phaseで検証可能な成果物を残す
- 人間承認を維持したまま、手動から段階的に自動化する
- 特定AIベンダーへ過度に依存しない
- Phase完了前に完了条件と管理側Source of Truthを同期する

## 3. Phase 1: Foundation

### 目的

Claude Codeと人間が要件・設計・ルールを読み、安全に開発を開始できる土台を完成させる。

### 対応要件

- FR-001〜FR-007
- NFR-001〜NFR-006の文書・運用基盤

### 成果物

- README
- requirements-v0.1
- architecture-v0.1
- development-rules
- roadmap-v0.1
- execution-plan-v0.1
- runbook-v0.1
- ai-routing
- orchestrator
- workspace
- CLAUDE.md
- Claude Code初期化プロンプト
- gitignore
- Issueテンプレート
- Pull Requestテンプレート
- 最初の開発Issue

### 完了条件

- 主要文書が役割と優先順位を持ち、相互に矛盾していない
- Claude Codeが目的、対象範囲、承認条件、作業手順を説明できる
- 秘密情報の誤コミット対策がある
- Issue・PRの標準入力形式がある
- Phase 2で使う最初のIssueが登録されている

## 4. Phase 2: Local Development Loop

### 目的

ChatGPTによる要件・設計、Claude Codeによる調査・実装・検証、GitHubによる履歴管理、engineering-career-hqによる管理記録を1つの再現可能な流れとして実証する。

### 対応要件

- FR-001〜FR-007
- NFR-001〜NFR-004

### 成果物

- 実行済みのIssue
- Feature Branch
- Commit
- Pull Request
- Review結果
- Merge結果
- fragment、daily-log、progress、review-taskの同期記録

### 完了条件

- 1件のIssueを調査、計画、承認、実装、検証、PR、レビュー、マージ、管理側同期まで完走できる
- 実行コマンド、変更ファイル、検証結果、失敗、未解決事項が追跡できる
- 承認前に変更が行われていない
- 失敗時に原因と次の対応を報告できる

## 5. Phase 3: AI Platform Core

### 目的

Task、状態、承認、実行履歴、Decision、Error、Next Actionを管理する最小アプリケーションを構築する。

### 対応要件

- FR-002、FR-004、FR-006〜FR-009
- NFR-001〜NFR-006

### 主な機能

- Task作成と状態遷移
- Task・Execution・Approval・Decision・Errorの永続化
- 再起動後の状態復元
- ローカルファイルの許可範囲確認
- 構造化ログ

### 完了条件

- DRAFTからCOMPLETEDまたは例外状態まで遷移できる
- 各Taskに承認・実行・エラー履歴が関連付く
- 再起動後に状態を復元できる
- Unit Testと基本的なセキュリティ検証が成功する

## 6. Phase 4: Orchestration

### 目的

複数AI、ローカルツール、GitHubを能力・Intent・Riskに基づいて連携させる。

### 対応要件

- FR-002〜FR-010

### 主な機能

- Intent分類とTask分解
- Agent・Tool選択
- 承認ゲート
- Retry、Timeout、停止
- GitHub Issue・PR同期
- Provider Adapter

### 完了条件

- 1つの要求を依存関係付きTaskへ分解できる
- 危険操作の前に停止できる
- Retry上限後に安全にFAILEDまたはBLOCKEDへ遷移できる
- Providerを差し替えてもCore状態モデルを維持できる

## 7. Phase 5: Voice and Meeting Notes

### 目的

音声会話から要求、議事録、Decision、Taskを生成し、Workspaceへ保存する。

### 対応要件

- FR-011

### 完了条件

- 音声からText Logを生成できる
- 会話終了後に要約、Decision、Taskを抽出できる
- 保存先Workspaceを選択できる
- 保存前に内容と送信先を確認できる

## 8. Phase 6: Knowledge Platform

### 目的

会話、設計、ログ、コードをProject境界と権限を維持して検索・再利用する。

### 対応要件

- FR-007、FR-012

### 完了条件

- 自然言語で横断検索できる
- 検索結果に出典が表示される
- Workspace間の情報混在を制御できる
- アクセス権のない情報を結果へ含めない

## 9. Phase 7: Productization

### 目的

個人用基盤から、他ユーザーが安全に利用できるプロダクトへ発展させる。

### 対応要件

- FR-013
- 製品化に必要な追加要件

### 完了条件

- 複数ユーザーの認証・認可・データ分離が機能する
- Setup、Upgrade、Backup、障害対応手順がある
- 利用規約、Privacy、課金、Supportが定義される
- 他ユーザー環境で受入テストが成功する

## 10. Phase依存関係

~~~text
Phase 1 Foundation
  ↓
Phase 2 Local Development Loop
  ↓
Phase 3 AI Platform Core
  ↓
Phase 4 Orchestration
  ├─ Phase 5 Voice and Meeting Notes
  └─ Phase 6 Knowledge Platform
       ↓
Phase 7 Productization
~~~

Phase 5とPhase 6はPhase 4完了後に独立して進められる。Phase 7はCore、Orchestration、VoiceまたはKnowledgeの必要範囲が安定した後に開始する。

## 11. 現在地

~~~text
Current phase: Phase 1 Foundation
Status: IN_PROGRESS
~~~

次の優先作業:

1. Foundation文書・安全基盤・GitHubテンプレートをmainへ反映する
2. Phase 1のEpic、Milestone、Issue、Task構造を登録する
3. Claude Codeで変更なしの初回調査を実施する
4. Phase 1完了監査を行う
5. Phase 2でIssue #1の開発ループを完走する
