# Workspace Specification

## 1. 目的

Workspaceは、用途ごとの会話、知識、ファイル、タスク、ルール、実行履歴を分離して管理する単位である。

音楽制作、SRE学習、Kubernetes / CKA、仕事、副業開発などを同じAI Platform上で扱いながら、コンテキストの混在と誤操作を防ぐ。

## 2. Workspaceの例

```text
workspaces/
├── ai-platform/
├── kubernetes-cka/
├── sre-learning/
├── krump-music/
├── work-consulting/
└── side-business/
```

## 3. 各Workspaceが持つ情報

- `workspace.md`: 目的、範囲、ルール
- `context.md`: 背景、現在地、主要判断
- `tasks/`: タスクと状態
- `decisions/`: 設計判断記録
- `sessions/`: 会話、議事録、要約
- `knowledge/`: 参照資料と検索対象
- `logs/`: 実行ログ、エラー、テスト結果
- `artifacts/`: 生成物への参照

## 4. Repositoryとの関係

WorkspaceとRepositoryは同一ではない。

- Workspaceは目的と文脈の単位
- Repositoryはコード・文書のバージョン管理単位
- 一つのWorkspaceが複数Repositoryを参照する場合がある
- 一つのRepositoryを複数Workspaceから利用する場合がある

例:

```text
Workspace: kubernetes-cka
  ├─ 管理・進捗: engineering-career-hq/projects/kubernetes
  ├─ ローカル演習: kind cluster
  └─ 関連実装: 必要時のみ対象Repository
```

## 5. Source of Truth

Workspaceは、情報の正本を勝手に複製しない。

- GitHub全体のAI入口と共通ルール: `YDTNK/00-ai-start-here`
- 学習・キャリア・進捗: `YDTNK/engineering-career-hq`
- AI Platform設計と実装: `YDTNK/ai-platform`
- 各種実装: 対象の実装Repository

Workspace側には、正本のパスと必要最小限の要約を保持する。

## 6. コンテキスト読み込み順序

1. Workspace定義
2. Source of Truthへの参照
3. 現在のタスク
4. 最新の決定事項
5. 必要な過去セッション
6. 対象Repositoryの設計・コード

無関係なWorkspaceやRepositoryを最初から一括読み込みしない。

## 7. セッション記録

会話終了時または重要判断時に以下を保存する。

- 日時
- 目的
- 要点
- 決定事項
- 未決事項
- ToDo
- 参照したファイル
- 実行した操作
- 次回開始地点

## 8. セキュリティ

- Workspaceごとにアクセス可能なパスを制限する
- ホームディレクトリ全体を常時許可しない
- 機密情報はKnowledge StoreやGitへ保存しない
- 外部送信前にデータ分類と承認を行う
- 個人情報・業務情報・公開情報を区別する

## 9. 初期運用

v0.1では、専用アプリやデータベースを作らず、既存RepositoryとMarkdownをWorkspaceとして扱う。

最初のWorkspaceは以下とする。

```text
name: ai-platform
source_of_truth: YDTNK/ai-platform
global_entrypoint: YDTNK/00-ai-start-here
purpose: AI連携基盤の設計・実装・運用
```

## 10. 将来拡張

- ベクトル検索
- セッション横断検索
- 音声文字起こし
- 自動議事録
- Workspaceごとの権限
- GUIでの切り替え
- 複数ユーザー対応

## 11. 完了条件

- WorkspaceとRepositoryの違いが定義されている
- 正本の重複を防げる
- 用途ごとにコンテキストを分離できる
- セッションから決定事項と次タスクを復元できる
- アクセス範囲をWorkspace単位で制御できる
