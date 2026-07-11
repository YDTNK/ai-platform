# AI Platform要件定義 v0.1

## 1. 文書の目的

本書は、AI Platformが解決する課題、対象範囲、必要な機能、品質条件、制約、完成条件を定義する最上位のプロダクト要件書である。

設計・実装・Issue・Pull Requestは本書へ追跡可能でなければならない。本書と下位文書が矛盾する場合は、承認された最新の本書を優先し、必要な下位文書を更新してから実装する。

## 2. プロダクトビジョン

ユーザーが音声またはテキストで目的を伝えると、AI Platformが要件整理、設計、タスク分解、担当AI・ツール選択、承認、実装、検証、記録、報告までを安全で再現可能な流れとして支援する。

特定のAIサービスへ固定せず、ChatGPT、Claude Code、GitHub、将来のGemini、MCP、ローカルツールを能力単位で組み替えられる個人向けAI協調開発基盤を目指す。

## 3. 解決する課題

- 会話、設計、コード、Issue、進捗記録が分散し、次のセッションで現在地を復元しにくい
- AIごとの得意分野、権限、承認条件が曖昧になりやすい
- AIが変更範囲を超えたり、危険な操作を無承認で実行したりするリスクがある
- 実行コマンド、判断理由、テスト結果、失敗内容が一貫した形式で残らない
- 特定AIやチャット履歴だけへ依存すると、サービス変更時に運用を継続しにくい
- Repository間のSource of Truthが曖昧だと、情報重複・更新漏れ・消失が発生する

## 4. 対象ユーザー

### v0.1

- YDTNK本人
- ChatGPTなどの設計・レビューAI
- Claude Codeなどの実装エージェント

### 将来

- 同様のAI協調開発を利用する個人
- 複数Workspaceや複数ユーザーを管理する利用者

## 5. ユースケース

### UC-01 要件から実装まで進める

1. ユーザーが目的を伝える
2. ChatGPTが要件、制約、完了条件を整理する
3. GitHub Issueへ実行単位を登録する
4. Claude Codeが調査と計画を提示する
5. 人間が承認する
6. Claude Codeが実装・テスト・差分確認を行う
7. Pull Requestを作成する
8. ChatGPTまたは人間がレビューする
9. マージ後に管理側Source of Truthを更新する

### UC-02 複数AIへ役割を振り分ける

依頼のIntent、必要能力、リスク、利用可能なツールを判定し、設計、調査、実装、レビューを適切なAIまたはツールへ割り当てる。

### UC-03 作業を安全に停止・再開する

承認待ち、失敗、ブロック、キャンセル状態を記録し、別セッションでも入力、出力、判断、未解決事項、次の操作を復元できるようにする。

### UC-04 Workspaceを分離する

AI Platform、SRE学習、Kubernetes、音楽制作などの文脈、ファイル、権限、履歴を混在させずに扱う。

## 6. 機能要件

| ID | 要件 | 優先度 | 初期実現方法 |
|---|---|---|---|
| FR-001 | 要求から目的・制約・完了条件を定義できる | MUST | ChatGPT + Markdown |
| FR-002 | 要求を依存関係付きTaskへ分解できる | MUST | Issue + Task checklist |
| FR-003 | Intentと能力に基づき担当AI・ツールを選択できる | MUST | 手動ルーティング規則 |
| FR-004 | 危険操作の前に人間承認で停止できる | MUST | CLAUDE.md + Runbook |
| FR-005 | Branch・Commit・Pull Requestで変更履歴を管理できる | MUST | GitHub |
| FR-006 | 実行コマンド、変更ファイル、テスト結果、エラーを記録できる | MUST | PR + 管理側daily-log |
| FR-007 | ProjectごとのSource of TruthとWorkspaceを識別できる | MUST | 00-ai-start-here + workspace仕様 |
| FR-008 | 失敗時に再試行回数を制限し、安全に停止できる | SHOULD | Orchestrator仕様、将来コード化 |
| FR-009 | AI・モデル・ツールを差し替えられる | SHOULD | 能力ベースのAdapter設計 |
| FR-010 | GitHub Issue・PRとTask状態を同期できる | SHOULD | Phase 4で実装 |
| FR-011 | 音声から要求・議事録・Taskを生成できる | COULD | Phase 5で実装 |
| FR-012 | 過去文書・会話・コードを出典付きで検索できる | COULD | Phase 6で実装 |
| FR-013 | 複数ユーザーとデータ分離へ対応できる | COULD | Phase 7で実装 |

## 7. 非機能要件

### NFR-001 安全性

- 人間を最終意思決定者とする
- 削除、大量上書き、外部送信、秘密情報アクセス、デプロイ、クラウド・DB変更、強制Pushは明示承認を必須とする
- APIキー、Token、Password、秘密鍵、個人情報をGitへ保存しない
- 最小権限とProject単位のアクセスを原則とする

### NFR-002 追跡可能性

- 要件 → 設計 → Roadmap → Task → Issue → PR → Commit → 管理記録を追跡できる
- 実行済みと未実行を区別し、未検証事項を明記する
- 情報移動時は保存先を先に作り、情報を消失させない

### NFR-003 再現性

- 別セッションでも必読文書、Issue、最新進捗から作業を再開できる
- 実行手順と確認方法をRunbookへ記録する
- 存在しないコマンド、依存関係、テストを推測して実行しない

### NFR-004 保守性

- 特定AIベンダー固有機能をCoreへ直接固定しない
- 小さな変更単位と明確な責務境界を維持する
- 共通ルールを複数Repositoryへ全文複製しない

### NFR-005 可観測性

- Task状態、承認、実行結果、失敗理由、次の操作を確認できる
- 将来の自動化では構造化ログと相関IDを利用できる設計にする

### NFR-006 可搬性

- 初期開発環境はmacOSを対象とする
- 将来はLinuxおよびHosted環境へ拡張可能な構成とする
- ローカル固有パスを設計の前提へ固定しない

## 8. Source of Truth要件

~~~text
YDTNK/00-ai-start-here
= GitHub全体の入口、Repositoryルーティング、共通AI運用ルール

YDTNK/engineering-career-hq/projects/ai-platform
= Project管理、進捗、daily-log、fragment、review-task

YDTNK/ai-platform
= 要件、Architecture、実装仕様、コード、テスト、Issue、PR、Commit
~~~

本Repositoryに管理側daily-reportを重複作成しない。

## 9. v0.1の対象範囲

### 対象

- 最上位要件と設計文書
- 安全なClaude Code開発ルール
- AI routing、Orchestrator、Workspaceの仕様
- GitHub Issue・Branch・Commit・PRによる手動開発ループ
- 承認ゲート、ログ項目、エラー時の停止規則
- GitHub運用テンプレートと秘密情報の誤コミット対策
- 管理側Source of Truthとの手動同期

### 対象外

- 完全自動Orchestrator
- 本番デプロイ自動化
- 無制限のローカルファイルアクセス
- 複数ユーザー対応、認証、課金
- 高度な音声UI
- Vector DatabaseやKnowledge検索の本実装
- 外部サービスへの無承認操作

## 10. 制約

- 人間承認を省略しない
- ChatGPT、Claude Code、GitHubの現行利用範囲から段階的に開始する
- 技術スタックはCore実装要件が確定するまで固定しない
- Project管理情報を実装Repositoryへ重複させない
- GitHub共通言語ポリシーに従う
- mainへ直接Commitしない

## 11. 完成の定義

### Foundation完成

- 要件、Architecture、Roadmap、Execution Plan、Runbook、AI仕様が整合している
- Claude Codeが目的、制約、必読順、承認条件を説明できる
- gitignore、Issueテンプレート、PRテンプレートが存在する
- 秘密情報がGitへ含まれていない
- 最初のIssueが登録されている

### 開発ループ完成

- 1件のIssueについて、要件整理、事前調査、計画承認、実装、検証、PR、レビュー、マージ、管理側同期を完走できる
- 実行コマンド、変更ファイル、テスト結果、未解決事項が追跡できる

### AI Platform Core完成

- Taskと状態遷移を永続化できる
- 承認履歴、実行履歴、Decision、Error、Next ActionをTaskへ関連付けられる
- 再起動後に状態を復元できる

### プロダクト完成

- Roadmap Phase 1〜7の完了条件を満たす
- 他ユーザーが安全にセットアップできる
- 認証、認可、データ分離、運用、障害対応、Privacy要件が定義・検証されている

## 12. 受入基準

- MUST要件が対応Phaseと成果物へ割り当てられている
- 下位文書が本書を参照している
- 完了条件が客観的に確認できる
- 対象外が明記されている
- 既存GitHub責務と矛盾しない
- 情報消失や重複するSource of Truthがない

## 13. 実装技術制約

採用技術の正本:

~~~text
docs/adr/0001-core-technology-stack.md
~~~

実装手順の正本:

~~~text
docs/implementation-guide-v0.1.md
~~~

公式調査記録:

~~~text
docs/research/technology-research-2026-07-11.md
~~~

- CoreはPython 3.12を使用する
- Dependencyはpyproject.tomlとuv.lockで再現する
- Domain層をProvider SDKから独立させる
- State・Approval・Retry上限をLLMへ委任しない
- Provider SDKとMCPはAdapter境界で接続する
- SQLiteから開始し、Alembic Migrationを維持してPostgreSQLへ移行可能にする
- Test・Static Check・Trace・CIをPhase 3から必須化する

## 14. 変更管理

要件変更は次の順序で行う。

1. 本書を更新し、変更理由と影響範囲を明記する
2. Architectureと関連仕様を更新する
3. RoadmapとExecution Planを更新する
4. Issueを作成または更新する
5. 承認後に実装する
6. テスト、PR、レビュー、管理側同期を行う
