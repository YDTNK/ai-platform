# AI Platform Execution Plan v0.1

## 1. 目的

本ドキュメントは、各フェーズを実行可能なToDoへ分解し、担当、入力、作業内容、確認方法、完了条件を定義する。

## 2. 共通タスク形式

各タスクは以下の形式で管理する。

- Task ID
- 目的
- 入力
- 担当
- 実施内容
- 実行コマンドまたはAI向けプロンプト
- 期待結果
- 確認方法
- 完了条件
- リスク
- 次のアクション

## 3. Phase 1: Foundation

### FND-001 リポジトリ状態確認

**目的**

現在のリポジトリ構成、ブランチ、変更状態を確認する。

**担当**

Claude Code

**コマンド**

```bash
git status
git branch --show-current
find . -maxdepth 3 -type f | sort
```

**期待結果**

- 現在のブランチが確認できる
- 未コミット変更の有無が確認できる
- 主要ファイル一覧が取得できる

**完了条件**

調査結果が作業ログへ記録されている。

---

### FND-002 基本文書確認

**目的**

Architecture、Development Rules、Roadmapを読み、矛盾や不足を確認する。

**入力**

- `docs/architecture-v0.1.md`
- `docs/development-rules.md`
- `docs/roadmap-v0.1.md`

**Claude Code向けプロンプト**

```text
上記3ファイルを読み、以下を報告してください。
1. プロジェクトの目的
2. v0.1の対象範囲
3. 実装前に守るルール
4. 矛盾または不足
5. 次に作成すべきファイル
この段階ではファイルを変更しないでください。
```

**完了条件**

Claude Codeがプロジェクトの目的と制約を正しく説明できる。

---

### FND-003 `.gitignore` 確認・作成

**目的**

秘密情報、依存関係、生成物、一時ファイルの誤コミットを防ぐ。

**コマンド**

```bash
test -f .gitignore && cat .gitignore || touch .gitignore
```

**最低限の候補**

```gitignore
.env
.env.*
!.env.example
.DS_Store
node_modules/
.venv/
__pycache__/
*.pyc
dist/
build/
coverage/
*.log
```

**確認コマンド**

```bash
git status --short
```

**完了条件**

秘密情報と主要な生成物がGit管理対象外になっている。

---

### FND-004 Claude Code初期化プロンプト作成

**目的**

Claude Codeが毎回同じルールで調査、計画、承認、実装、報告を行えるようにする。

**成果物**

`prompts/claude-code-initialization.md`

**完了条件**

プロンプトに以下が含まれる。

- 必読ファイル
- 調査優先
- 承認前の変更禁止
- セキュリティ制約
- テストと報告形式
- Git運用

---

### FND-005 Issueテンプレート作成

**目的**

開発タスクを一貫した形式で登録する。

**成果物**

`.github/ISSUE_TEMPLATE/feature.yml`

**含める項目**

- 目的
- 背景
- 要件
- 完了条件
- 対象外
- リスク
- テスト方法

**完了条件**

GitHubで新規Issue作成時にテンプレートを選択できる。

---

### FND-006 Pull Requestテンプレート作成

**成果物**

`.github/pull_request_template.md`

**含める項目**

- 変更目的
- 変更内容
- 関連Issue
- 実行したテスト
- リスク
- レビュー観点
- チェックリスト

**完了条件**

新規PRにテンプレートが自動表示される。

---

### FND-007 最初のIssue作成

**タイトル案**

```text
feat: establish local development loop
```

**目的**

Claude Codeによる調査、計画、承認、実装、テスト、PR作成の最小ループを確立する。

**完了条件**

- Issueが作成される
- 完了条件が明記される
- 対象外が明記される

## 4. Phase 2: Local Development Loop

### LDL-001 ローカルへClone

```bash
cd <任意の作業ディレクトリ>
git clone https://github.com/YDTNK/ai-platform.git
cd ai-platform
```

**確認**

```bash
git remote -v
git status
```

---

### LDL-002 Feature Branch作成

```bash
git switch -c feature/local-development-loop
```

**確認**

```bash
git branch --show-current
```

---

### LDL-003 Claude Code起動

```bash
claude
```

**初回指示**

```text
prompts/claude-code-initialization.md を読み、その指示に従ってください。
まずリポジトリを調査し、変更は行わず、調査結果と実装計画だけを提示してください。
```

---

### LDL-004 計画承認

ユーザーは以下を確認する。

- 変更対象ファイル
- 目的との整合性
- 影響範囲
- リスク
- テスト方法
- 不要な変更が含まれていないこと

承認後のみ実装へ進む。

---

### LDL-005 実装・テスト

プロジェクトの技術スタック確定後、以下を実行する。

```bash
# 例: Node.js
npm install
npm run lint
npm test
npm run build

# 例: Python
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pytest
```

**注意**

実際に存在するコマンドのみ実行する。存在しないスクリプトを推測して実行しない。

---

### LDL-006 差分確認

```bash
git status --short
git diff --stat
git diff
```

**完了条件**

変更内容をユーザーがレビューできる。

---

### LDL-007 CommitとPush

```bash
git add <対象ファイル>
git commit -m "feat: establish local development loop"
git push -u origin feature/local-development-loop
```

---

### LDL-008 Pull Request作成

GitHub上でPRを作成し、以下を記載する。

- 目的
- 変更内容
- テスト結果
- リスク
- 未解決事項
- 関連Issue

## 5. Phase 3以降の実行方針

Phase 3以降は、各機能を以下の順序で進める。

1. 要件定義
2. Architecture更新
3. ADRまたはDecision Log作成
4. Issue作成
5. Claude Codeによる調査
6. 実装計画
7. 人間の承認
8. Feature Branch作成
9. 実装
10. テスト
11. セキュリティ確認
12. PR作成
13. レビュー
14. マージ
15. 作業ログ更新

## 6. Phase 1の残タスク

- [x] Architecture v0.1
- [x] Development Rules
- [x] Roadmap v0.1
- [x] Execution Plan v0.1
- [ ] Runbook v0.1
- [ ] Claude Code初期化プロンプト
- [ ] `.gitignore` 確認
- [ ] Issueテンプレート
- [ ] Pull Requestテンプレート
- [ ] 最初のIssue
- [ ] Claude Codeによる調査

## 7. 完了定義

Phase 1は以下をすべて満たした場合に完了とする。

- 必要な設計書とルールがGitHubに保存されている
- Claude Codeの初期化手順が再現可能である
- 秘密情報の誤コミット対策がある
- IssueとPRの標準形式がある
- 最初の開発Issueが登録されている
- Claude Codeが変更なしのリポジトリ調査を正常に完了している