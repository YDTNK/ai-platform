# AI Platform

## ミッション

AI Platformは、AIを活用してソフトウェア開発を進めるための基盤です。

ユーザーが音声またはテキストで目的を伝えるだけで、要件整理、設計、手順書作成、実装、テスト、デバッグ、記録、報告までを一連の流れとして進められる環境を目指します。

会話・コード・知識をつなぎ、設計から実装、記録、報告、デバッグまでを支援する個人向けAIプラットフォームです。

## GitHub全体における位置づけ

```text
YDTNK/00-ai-start-here
  └─ GitHub全体のAI入口・Repositoryルーティング・共通AI運用ルールの正本

YDTNK/ai-platform
  └─ AI連携、オーケストレーション、Workspace、実行基盤、プロダクト開発記録の正本

YDTNK/engineering-career-hq
  └─ 学習、キャリア、進捗、ロードマップ、daily-logの正本

各実装Repository
  └─ コード、CI/CD、デプロイ、成果物の正本
```

AI Platformは `00-ai-start-here` を置き換えません。`00-ai-start-here` が「どこを見るか」を決め、AI Platformが「どう分解し、どのAI・ツールで実行し、どう記録するか」を担当します。

AI Platform固有の詳細な開発履歴は `reports/` に保存します。学習・キャリア・全体進捗へ影響する節目だけを、必要に応じて `engineering-career-hq` へ要約・参照として同期します。

## 目的

- 音声やテキストで要件を伝える
- AIが要件整理、設計、手順書作成を行う
- Claude Codeなどの実装エージェントがローカルファイルを編集する
- テスト、デバッグ、Git操作、作業ログの保存までを一連の流れとして管理する
- 複数のAIやツールを用途に応じて使い分ける
- 特定のAIサービスに依存しすぎず、将来ほかのモデルへ差し替えられる構成にする

## 想定する役割

- **ChatGPT**: 会話、要件整理、設計、レビュー、説明
- **Claude Code**: 実装、テスト、デバッグ、ローカルファイル操作
- **Gemini / Search Agent**: 最新情報、公式情報、Google系連携
- **Orchestrator**: 意図分類、タスク分解、実行順序、承認、状態管理
- **Workspace**: 用途ごとの文脈、知識、タスク、記録の分離
- **GitHub**: ソースコード、設計書、Issue、Pull Request、履歴管理

## 初期スコープ

1. AI Platformの設計書を作成する
2. Claude Codeで安全にローカル開発できる構成を整える
3. GitHubと連携して変更履歴を管理する
4. AIの振り分けルールを定義する
5. Orchestratorの状態・承認・エラー処理を定義する
6. WorkspaceとSource of Truthの関係を定義する
7. 実行ログ、決定事項、エラー、次の作業を記録する
8. 将来的に音声操作とリアルタイム要約を統合する

## 主要ドキュメント

| ファイル | 役割 |
|---|---|
| `docs/architecture-v0.1.md` | 全体アーキテクチャ |
| `docs/development-rules.md` | 開発・Git・セキュリティルール |
| `docs/roadmap-v0.1.md` | フェーズとマイルストーン |
| `docs/execution-plan-v0.1.md` | ToDoと実行計画 |
| `docs/runbook-v0.1.md` | コマンドレベルの手順 |
| `docs/ai-routing.md` | AI・ツールの振り分けルール |
| `docs/orchestrator.md` | タスク、状態、承認、エラー処理 |
| `docs/workspace.md` | 文脈・知識・Repositoryの管理単位 |
| `docs/reporting-policy.md` | fragment、daily report、Repository間同期の運用 |
| `CLAUDE.md` | Claude Code向けプロジェクト固有ルール |
| `prompts/claude-code-initialization.md` | Claude Code初期調査用プロンプト |

## ディレクトリ構成

```text
.
├── CLAUDE.md
├── README.md
├── docs/       設計書・仕様書・議事録
├── prompts/    AI向けプロンプト
├── reports/    AI Platformのdaily report・fragment・テンプレート
├── scripts/    開発・運用スクリプト
└── src/        アプリケーションコード
```

## Reporting

```text
reports/daily/YYYY-MM-DD.md
= その日のAI Platform開発レポート

reports/daily/fragments/YYYY-MM-DD/NN-topic.md
= 作業中の主要タスク単位の証跡
```

詳細は `docs/reporting-policy.md` と `reports/daily/README.md` を参照してください。

## 現在の開発フロー

```text
User
  ↓
ChatGPT: 要件整理・設計・レビュー
  ↓
Claude Code: 調査・計画・実装・テスト
  ↓
GitHub: Branch・Commit・Pull Request・履歴
  ↓
reports/: fragment・daily report・意思決定・次回作業
  ↓
ChatGPT / Human: 最終レビューと次タスク
```

現在は手動または半自動の連携を検証し、安定した開発ループと記録運用を確立してから自動オーケストレーションへ進みます。
