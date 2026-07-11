# AI Routing Specification

## 1. 目的

AI Platformが、依頼の内容に応じて最適なAI・ツール・実行経路を選択するための基本ルールを定義する。

本仕様は、特定ベンダー名を固定的な中心にせず、能力と役割によってルーティングする。

## 2. 基本原則

- 最終判断は人間が行う
- まず意図を分類し、その後にAIまたはツールを選ぶ
- 危険な操作は承認前に実行しない
- 一つのAIで完結させる必要はない
- 実行結果、根拠、失敗、引き継ぎ情報を記録する
- モデルやサービスは差し替え可能にする

## 3. 意図分類

| Intent | 内容 | 主な担当 |
|---|---|---|
| requirements | 要件整理、目的、完了条件 | ChatGPT系 |
| architecture | 設計、比較、技術選定 | ChatGPT系 + 必要に応じて検索 |
| research | 最新情報、公式資料、外部調査 | 検索能力を持つエージェント |
| implementation | コード、ファイル編集、ローカル操作 | Claude Code系 |
| test | テスト、Lint、Build、検証 | Claude Code系 |
| review | 設計・コード・差分レビュー | 実装担当と異なるAIを優先 |
| documentation | README、設計書、手順書 | ChatGPT系またはClaude系 |
| operations | Git、CI/CD、クラウド、デプロイ | 実行エージェント + 人間承認 |
| knowledge | 議事録、決定事項、検索、記録 | Knowledge Store |

## 4. 初期の役割分担

### ChatGPT

- 会話による要件ヒアリング
- 要件、制約、完了条件の整理
- アーキテクチャ設計
- Claude Code向け指示の生成
- 実装結果の説明とレビュー

### Claude Code

- リポジトリ調査
- ローカルファイルの読み書き
- 実装、テスト、デバッグ
- Git差分の作成
- 承認された範囲でのCommit、Push、PR作成

### Geminiまたは検索エージェント

- 最新情報の調査
- 公式ドキュメントの確認
- Google系サービスとの連携

### GitHub

- ソースコードと設計書の正本
- Issue、Pull Request、履歴、CIの管理

## 5. ルーティングフロー

```text
User Request
    ↓
Intent Classification
    ↓
Context and Risk Check
    ↓
Task Decomposition
    ↓
Agent / Tool Selection
    ↓
Human Approval if Required
    ↓
Execution
    ↓
Review and Verification
    ↓
Log / Decision / Next Action
```

## 6. 複合タスク

複合タスクは、単一AIへ丸投げせず、依存関係を明示して分割する。

例:

```text
最新のKubernetes変更点を調査し、学習教材を更新する

1. 検索エージェント: 公式情報を調査
2. ChatGPT: 変更点を整理し教材構成を設計
3. Claude Code: 対象ファイルを更新しテスト
4. ChatGPTまたは人間: 差分レビュー
5. GitHub: PRと記録を保存
```

## 7. 承認が必要な操作

- ファイル削除
- 大量上書き
- 外部サービスへのデータ送信
- パッケージの大規模更新
- クラウドリソース変更
- データベース変更
- デプロイ
- Gitの強制Push
- 機密情報を扱う処理

## 8. フォールバック

- 担当AIが利用できない場合は、同じ能力を持つ代替AIへ切り替える
- 外部検索が失敗した場合は、未確認事項を明示する
- 実装が失敗した場合は、エラー、再現条件、変更差分を保存する
- 自信が低い場合は自動実行せず、人間へ確認する

## 9. v0.1の範囲

v0.1では自動ルーティングエンジンは実装せず、文書化されたルールに基づく手動または半自動運用を行う。

## 10. 完了条件

- 意図分類が定義されている
- 各AIとツールの責務が分離されている
- 承認条件が明示されている
- 複合タスクの引き継ぎ方法が定義されている
- 特定AIが変更されても設計を維持できる
