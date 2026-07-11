# AI Platform Architecture v0.1

## 1. ビジョン

AI Platformは、ユーザーが音声またはテキストで目的を伝えるだけで、要件整理、設計、手順書作成、実装、テスト、デバッグ、記録、報告までを一連の流れとして進められる開発基盤を目指します。

## 2. 要件との関係

本Architectureはdocs/requirements-v0.1.mdで定義された機能要件・非機能要件を実現する構造を定義する。

要件変更時は、Requirements → Architecture → Roadmap → Execution Plan → Issue → Implementationの順で影響を反映する。

主な対応関係:

| 要件 | Architecture上の対応 |
|---|---|
| FR-001、FR-002 | ChatGPT、Orchestrator、GitHub Issue |
| FR-003 | AI Routing |
| FR-004 | Approval Gate |
| FR-005 | GitHub Branch・Commit・Pull Request |
| FR-006 | Execution Log・管理側daily-log |
| FR-007 | Workspace・Source of Truth |
| FR-008〜FR-010 | Orchestrator Core・Provider Adapter |
| NFR-001〜NFR-006 | Security、Logging、State、Adapter、Runbook |

## 3. 基本方針

- 人間が最終的な意思決定者であること
- 変更前に計画と影響範囲を提示すること
- 危険な操作は自動実行せず、承認を必須にすること
- 実行内容、結果、エラー、判断理由を記録すること
- 特定のAIモデルに強く依存しないこと
- 小さな単位で実装し、常にテスト可能な状態を維持すること

## 4. 想定アーキテクチャ

```text
User
  ↓
Voice / Text Interface
  ↓
AI Platform Orchestrator
  ├─ ChatGPT: 要件整理・設計・レビュー・説明
  ├─ Claude Code: 実装・テスト・デバッグ・Git操作
  ├─ Local Files: ソースコード・資料・音源・ログ
  ├─ GitHub: Issue・PR・履歴・CI
  └─ Knowledge Store: 議事録・決定事項・学習記録
```

## 5. 各コンポーネントの役割

### 5.1 ChatGPT

- 会話による要件ヒアリング
- 曖昧な指示の整理
- 設計案と選択肢の提示
- Claude Code向け実装指示の生成
- 実装結果のレビュー
- 結果の分かりやすい説明

### 5.2 Claude Code

- 対象リポジトリの調査
- ファイルの読み書き
- 実装とリファクタリング
- テスト、Lint、ビルド
- エラー調査とデバッグ
- Git差分の作成

### 5.3 Orchestrator

- タスク分解
- 担当AIの選択
- 実行順序の管理
- 状態と依存関係の管理
- 承認待ちの制御
- 再試行とエラー処理
- 作業ログの保存

### 5.4 GitHub

- ソースコード管理
- Issueによるタスク管理
- Pull Requestによる変更レビュー
- GitHub Actionsによる自動テスト
- 設計書、議事録、運用記録の保存

## 6. v0.1の対象範囲

### 実装するもの

- リポジトリの基本構成
- AI Platformの設計書
- Claude Code向けの開発ルール
- タスク、実行ログ、決定事項の記録形式
- 最小限のオーケストレーション設計

### まだ実装しないもの

- 完全自動の本番デプロイ
- 無制限のローカルファイルアクセス
- 課金処理
- 複数ユーザー対応
- 高度な音声UI
- 外部サービスへの無承認操作

## 7. セキュリティ方針

- APIキーや秘密情報をGitHubへ保存しない
- `.env` はGit管理対象外にする
- Claude Codeへは必要なプロジェクト単位でアクセスを許可する
- ホームディレクトリ全体やディスク全体を常時許可しない
- 削除、上書き、外部送信、デプロイは承認制にする
- 実行可能なコマンドを段階的に制限する
- 変更前後の差分を必ず確認する

## 8. 開発フロー

1. ユーザーが音声またはテキストで目的を伝える
2. ChatGPTが要件と完了条件を整理する
3. Orchestratorがタスクへ分解する
4. Claude Codeが調査計画を提示する
5. ユーザーが承認する
6. Claude Codeが実装とテストを行う
7. ChatGPTが結果をレビューする
8. GitHubへPR、ログ、決定事項を保存する
9. 次のタスクを提案する

## 9. 記録する情報

- 要件
- 完了条件
- 設計判断
- 実行したコマンド
- 変更ファイル
- テスト結果
- 発生したエラー
- 解決方法
- 未解決事項
- 次のアクション

## 10. Architecture境界

本書はComponent責務と関係を定義する。

- Phaseと完成順序はroadmap-v0.1.mdで定義する
- 実行Taskはexecution-plan-v0.1.mdで定義する
- 操作手順はrunbook-v0.1.mdで定義する
- IntentとAgent選択はai-routing.mdで定義する
- 状態・承認・Error処理はorchestrator.mdで定義する
- ContextとSource of Truth分離はworkspace.mdで定義する

## 11. 現在の実装方針

現在はPhase 1 Foundationである。

1. Requirementsと主要文書の整合を完成させる
2. GitHub安全基盤とテンプレートを整備する
3. Epic・Milestone・Issue・Task構造を登録する
4. Claude Codeで変更なしの初回調査を行う
5. Phase 1完了後、Phase 2でIssue #1の開発ループを完走する

完全自動Orchestratorは、手動開発ループとCore状態モデルの検証後に実装する。
