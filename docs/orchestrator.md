# Orchestrator Specification

## 1. 目的

Orchestratorは、ユーザーの依頼を安全かつ再現可能なタスクへ分解し、AI・ツール・承認・状態・記録を統括するAI Platformの中核コンポーネントである。

## 2. 責務

- 依頼の意図分類
- 必要なコンテキストの特定
- タスク分解と依存関係管理
- 担当AI・ツールの選択
- 承認待ちの制御
- 実行状態の管理
- 再試行とエラー処理
- 実行ログ、決定事項、成果物の保存
- 最終報告と次アクションの生成

## 3. 非責務

- 人間の最終判断を代替すること
- 無承認で危険な操作を実行すること
- 根拠なく不足情報を補完すること
- 特定のAIベンダーへ依存すること

## 4. タスク状態

```text
DRAFT
  ↓
READY
  ↓
PLANNING
  ↓
WAITING_APPROVAL
  ↓
RUNNING
  ↓
VERIFYING
  ↓
COMPLETED
```

例外状態:

```text
BLOCKED
FAILED
CANCELLED
```

## 5. 標準フロー

1. ユーザーの依頼を受け取る
2. 目的、制約、完了条件を整理する
3. 必要なRepository、Workspace、Knowledgeを特定する
4. タスクへ分解する
5. 実行担当と順序を決定する
6. リスクと承認要否を判定する
7. 承認不要なら実行、必要なら待機する
8. 結果を検証する
9. ログ、決定事項、成果物を保存する
10. 最終報告と次のアクションを提示する

## 6. タスク定義の最小項目

```yaml
id: task-0001
title: string
intent: requirements|architecture|research|implementation|test|review|documentation|operations|knowledge
status: DRAFT
owner: human|chatgpt|claude-code|gemini|tool
inputs: []
outputs: []
dependencies: []
approval_required: true
risk_level: low|medium|high
completion_criteria: []
log_path: string
```

## 7. 承認ゲート

### 自動実行可能

- 読み取り専用調査
- 設計案の作成
- 変更を伴わないレビュー
- 既知の安全なテスト実行

### 明示承認が必要

- ファイル削除
- 大量変更
- 外部送信
- クラウド変更
- デプロイ
- データベース変更
- 強制Push
- 秘密情報へのアクセス

## 8. エラー処理

1. エラー内容を保存する
2. 実行したコマンドと入力を保存する
3. 再現可能性を確認する
4. 自動再試行の可否を判定する
5. 再試行する場合は回数を制限する
6. 解決しない場合はBLOCKEDまたはFAILEDへ遷移する
7. 人間向けに原因候補と選択肢を報告する

## 9. ログ

各実行で以下を保存する。

- 依頼原文
- 要件と完了条件
- 選択したAI・ツール
- 判断理由
- 実行コマンド
- 変更ファイル
- テスト結果
- エラー
- 承認履歴
- 未解決事項
- 次のアクション

## 10. GitHubとの関係

- Issue: タスクと完了条件
- Branch: 変更の隔離
- Commit: 論理的な変更単位
- Pull Request: レビューと承認
- Actions: 自動テスト
- Repository documents: 設計と運用の正本

## 11. v0.1の実装方針

v0.1では、GitHub Issue、Markdown、Claude Code、ChatGPTを使った手動オーケストレーションを採用する。

将来はLangGraph、独自状態機械、MCP、ジョブキューなどの採用を比較検討するが、技術選定は要件と検証結果に基づいて行う。

## 12. 完了条件

- 状態遷移が定義されている
- 承認ゲートが定義されている
- タスクの入力、出力、依存関係が記録できる
- 失敗時に安全に停止できる
- GitHub上で実行履歴を追跡できる
