# AI Platform

## ミッション

AI Platformは、会話、設計、実装、検証、GitHub、知識、進捗記録をつなぎ、複数のAIとツールが人間の承認下で協調できる個人向け開発基盤です。

ユーザーが音声またはテキストで目的を伝えると、要件整理、設計、Task分解、担当選択、実装、テスト、レビュー、記録、報告までを安全で再現可能な流れとして進められる状態を目指します。

## 最上位要件

プロダクトの目的、対象範囲、機能要件、非機能要件、制約、完成条件の正本は以下です。

~~~text
docs/requirements-v0.1.md
~~~

設計・Roadmap・Execution Plan・Issue・実装は、最上位要件へ追跡可能でなければなりません。

## GitHub全体における位置づけ

~~~text
YDTNK/00-ai-start-here
  └─ GitHub全体のAI入口・Repositoryルーティング・共通AI運用ルール

YDTNK/engineering-career-hq/projects/ai-platform
  └─ Project管理・進捗・daily-log・fragment・review-task

YDTNK/ai-platform
  └─ 要件・Architecture・実装仕様・コード・テスト・Issue・PR・Commit
~~~

AI Platformは00-ai-start-hereを置き換えません。00-ai-start-hereが「どこを見るか」を決め、AI Platformが「どう分解し、どのAI・ツールで実行するか」を扱います。

Projectの現在地、日次記録、判断履歴、次回タスクはengineering-career-hqで管理します。本Repositoryに並行daily-report構造を作成しません。

## 文書体系と優先順位

| 優先 | ファイル | 役割 |
|---:|---|---|
| 1 | docs/requirements-v0.1.md | 目的・要件・制約・完成条件 |
| 2 | docs/architecture-v0.1.md | 要件を満たす全体構造 |
| 3 | docs/roadmap-v0.1.md | 完成までのPhase・成果物・完了条件 |
| 4 | docs/execution-plan-v0.1.md | Phaseを実行可能なTaskへ分解 |
| 5 | docs/runbook-v0.1.md | 人間とClaude Codeが実行する手順 |
| 6 | docs/ai-routing.md | Intentと担当AI・ツール選択 |
| 6 | docs/orchestrator.md | Task状態・承認・エラー処理 |
| 6 | docs/workspace.md | 文脈・権限・Source of Truthの分離 |
| 7 | docs/development-rules.md | 開発・Git・品質・セキュリティ規則 |
| 8 | CLAUDE.md | Claude Codeが本Repositoryで守る指示 |
| 9 | prompts/claude-code-initialization.md | 初回調査用プロンプト |

矛盾がある場合は、承認された最新の上位文書を優先し、下位文書を整合させてから実装します。

## 要件から完成までの流れ

~~~text
Requirements
  ↓
Architecture
  ↓
Roadmap
  ↓
Execution Plan
  ↓
Epic / Milestone / Issue / Task
  ↓
Claude Code investigation and plan
  ↓
Human approval
  ↓
Implementation and verification
  ↓
Pull Request and review
  ↓
Merge
  ↓
engineering-career-hq synchronization
~~~

## 現在のPhase

~~~text
Phase: Phase 1 Foundation
Status: IN_PROGRESS
~~~

現在の目的は、安全で再現可能なChatGPT → Claude Code → GitHub開発ループを開始できる基盤を完成させることです。

現在地と次の作業は以下を正本とします。

~~~text
YDTNK/engineering-career-hq/projects/ai-platform/progress.md
~~~

## ディレクトリ構成

~~~text
.
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   └── feature.yml
│   └── pull_request_template.md
├── docs/
│   ├── requirements-v0.1.md
│   ├── architecture-v0.1.md
│   ├── roadmap-v0.1.md
│   ├── execution-plan-v0.1.md
│   ├── runbook-v0.1.md
│   ├── ai-routing.md
│   ├── orchestrator.md
│   ├── workspace.md
│   └── development-rules.md
├── prompts/
│   └── claude-code-initialization.md
├── scripts/
├── src/
├── .gitignore
├── CLAUDE.md
└── README.md
~~~

scriptsとsrcは実装開始前の予約領域です。技術スタック確定前に不要な依存関係や雛形を追加しません。

## 標準開発フロー

1. ChatGPTが要件、Architecture、完了条件を整理する
2. GitHubへEpic、Milestone、Issue、Taskを登録する
3. Claude Codeが変更なしの事前調査と実装計画を提示する
4. 人間が変更範囲、リスク、検証方法を承認する
5. Claude CodeがFeature Branchで実装・検証する
6. Pull Requestを作成する
7. ChatGPTまたは人間がレビューする
8. mainへマージする
9. engineering-career-hqのfragment、daily-log、progress、review-taskを必要に応じて更新する

## 安全原則

- mainへ直接Commitしない
- 秘密情報をGitへ保存しない
- 削除、大量変更、外部送信、秘密情報アクセス、デプロイ、クラウド・DB変更、強制Pushは明示承認を必要とする
- 実在しないコマンドやテストを推測して実行しない
- 情報を移動・削除する前に保存先と参照先を確認する
- 完全自動化の前に手動フローを検証する
