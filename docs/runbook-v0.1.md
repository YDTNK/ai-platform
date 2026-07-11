# AI Platform Runbook v0.1

## 1. 目的

本Runbookは、AI Platformの初期セットアップと日常開発を再現可能なコマンドレベルの手順として定義する。

## 2. 前提条件

- macOS
- Gitが利用可能
- GitHubアカウントへアクセス可能
- Claude Codeが利用可能
- リポジトリ: `https://github.com/YDTNK/ai-platform`

## 3. 初回セットアップ

### 3.1 Git確認

```bash
git --version
```

期待結果: Gitのバージョンが表示される。

### 3.2 作業ディレクトリへ移動

```bash
cd <現在利用している任意の開発用ディレクトリ>
pwd
```

既存のローカル構成を変更する必要はない。任意の場所へCloneする。

### 3.3 リポジトリをClone

```bash
git clone https://github.com/YDTNK/ai-platform.git
cd ai-platform
```

### 3.4 Remote確認

```bash
git remote -v
```

期待結果: `origin` が `YDTNK/ai-platform` を指している。

### 3.5 状態確認

```bash
git status
git branch --show-current
find . -maxdepth 3 -type f | sort
```

## 4. Claude Codeでの初回調査

### 4.1 リポジトリ直下で起動

```bash
claude
```

### 4.2 初期指示

以下をClaude Codeへ入力する。

```text
prompts/claude-code-initialization.md を読み、その指示に従ってください。

最初に以下を実施してください。
1. README.md を読む
2. docs/architecture-v0.1.md を読む
3. docs/development-rules.md を読む
4. docs/roadmap-v0.1.md を読む
5. docs/execution-plan-v0.1.md を読む
6. docs/runbook-v0.1.md を読む
7. 現在のリポジトリ構成を調査する

この段階ではファイル変更、パッケージ導入、Git操作を行わないでください。
調査結果、矛盾、不足、推奨する次の作業、リスクを報告してください。
```

### 4.3 調査結果の確認

以下を確認する。

- プロジェクトの目的を正しく理解している
- v0.1の対象範囲を説明できている
- セキュリティルールを理解している
- 勝手にファイルを変更していない
- 次の作業が小さな単位に分割されている

## 5. 日常の開発フロー

### 5.1 最新状態取得

```bash
git switch main
git pull --ff-only origin main
git status
```

### 5.2 Feature Branch作成

```bash
git switch -c feature/<task-name>
```

例:

```bash
git switch -c feature/local-development-loop
```

### 5.3 Claude Codeへ調査を依頼

```text
関連Issueと設計書を読み、実装前調査を行ってください。
この段階では変更しないでください。
以下を報告してください。
- 現状
- 変更対象
- 実装方針
- 影響範囲
- リスク
- テスト方針
- 完了条件
```

### 5.4 計画承認後に実装

```text
提示された計画を承認します。
承認された範囲だけを実装してください。
不要なリファクタリングは行わないでください。
実装後にテスト、差分確認、結果報告を行ってください。
```

### 5.5 状態と差分確認

```bash
git status --short
git diff --stat
git diff
```

### 5.6 テスト

実際の技術スタックとリポジトリに存在するスクリプトを確認してから実行する。

```bash
# package.json が存在する場合
cat package.json
npm run

# pyproject.toml が存在する場合
cat pyproject.toml

# Makefile が存在する場合
make help
```

存在するコマンドのみ実行する。

### 5.7 秘密情報確認

```bash
git diff --cached
find . -maxdepth 3 \( -name '.env' -o -name '*.pem' -o -name '*.key' \) -print
```

秘密情報が見つかった場合はCommitしない。

### 5.8 Commit

```bash
git add <対象ファイル>
git diff --cached --stat
git diff --cached
git commit -m "<type>: <summary>"
```

例:

```bash
git commit -m "docs: add Claude Code initialization prompt"
```

### 5.9 Push

```bash
git push -u origin HEAD
```

### 5.10 Pull Request

PRには以下を記載する。

- 目的
- 変更内容
- 関連Issue
- テスト結果
- セキュリティ確認
- リスク
- 未解決事項

## 6. エラー発生時

### 6.1 状態を保存

```bash
git status --short
git diff --stat
```

### 6.2 エラーを記録

以下を記録する。

- 実行したコマンド
- 完全なエラーメッセージ
- 発生時刻
- 再現手順
- 直前の変更

### 6.3 根拠のない再実行を止める

同じ失敗コマンドを繰り返さず、原因候補と確認手順を先に整理する。

### 6.4 Claude Codeへのデバッグ指示

```text
以下のエラーを調査してください。
この段階では修正を行わないでください。

報告内容:
1. エラーの要約
2. 再現条件
3. 原因候補
4. 確認コマンド
5. 影響範囲
6. 修正案
7. リスク
8. 推奨案
```

## 7. 危険操作の承認

以下は実行前に必ず明示的承認を得る。

- `rm` を使う削除
- 大量ファイルの上書き
- `git push --force`
- データベース変更
- クラウドリソース変更
- 外部サービスへの送信
- デプロイ
- 課金を伴う操作
- 権限変更

## 8. 作業終了時

```bash
git status
git log -1 --oneline
```

以下を報告する。

- 完了した作業
- 変更ファイル
- 実行したテスト
- テスト結果
- エラーと解決方法
- 未解決事項
- 次のアクション

## 9. 緊急停止

意図しない大量変更や危険な操作が疑われる場合は、追加操作を止める。

```bash
git status --short
git diff --stat
```

未コミット変更を破棄するコマンドは自動実行しない。状態を確認し、ユーザー承認後に対応する。

## 10. Runbook完了条件

- 別の作業環境でも同じ手順でCloneできる
- Claude Codeが変更なしの初回調査を実行できる
- Feature Branchで作業できる
- 差分、テスト、秘密情報を確認できる
- Commit、Push、PR作成まで再現できる
- エラー時に安全に停止・報告できる