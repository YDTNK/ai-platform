# ADR-0002: ChatGPT(Codex) — Claude Code ローカルMCPブリッジ

Status: ACCEPTED
Date: 2026-07-11

## Context

roadmap-v0.1.mdは本来、Phase 2で人間リレーによる「ChatGPT設計 → Claude Code実装」ループを1件検証してから、Phase 4 Orchestrationで複数AI連携を自動化する順序を定めていた（`docs/ai-routing.md` 9節、`docs/orchestrator.md` 11節）。

一方で、OpenAI Codex CLIは `codex mcp-server` サブコマンドでMCPサーバーとして起動でき、Claude Codeを含む任意のMCPクライアントから `codex` ツールとして呼び出せる。ローカル環境の調査で以下を確認した。

- Codex CLIは `npx -y @openai/codex mcp-server` で追加インストールなしに起動できる
- 認証は `codex login status` の結果 `Logged in using ChatGPT` で確認済み。ChatGPT Plus/Pro等のログインで動作し、API従量課金は不要
- `codex` MCPツールは `prompt` / `cwd` / `sandbox`(`read-only`|`workspace-write`|`danger-full-access`) / `approval-policy`(`untrusted`|`on-request`|`never`) を受け付け、指定した `cwd` 配下のローカルファイルを実際に読み書きできる
- 生のMCP stdioプロトコルで `initialize` → `tools/call` を直接実行し、`sandbox: read-only` かつ `approval-policy: never` のセッションが正しい権限プロファイル（`file_system: restricted, access: read`）で起動することを確認した
- 実際に `cwd: /Users/dimit/ai-platform` を指定して「README.mdを読んでプロジェクトの目的を日本語1文で要約する」タスクを実行し、Codexがローカルの`README.md`を読み取った上で正確な要約を返すことを確認した。レスポンスの `rate_limits` は `plan_type: "plus"` で、API従量課金ではなくChatGPT Plusログインのクレジットで動作していることも確認した

ユーザーは、Phase 2の人間リレー検証を経ずに直接この技術ブリッジを採用してよいと明示的に判断した。

## Decision

`ai-platform` リポジトリを起点とする作業では、Claude Codeをハブとし、Codex CLIをMCP経由のスポークとして接続する。

### 接続方法

~~~text
claude mcp add --transport stdio codex --scope project \
  -- npx -y @openai/codex mcp-server
~~~

- Scopeは `project`。リポジトリ直下の `.mcp.json` に保存し、Gitで追跡する
- 初回のみ、当該ディレクトリで `claude` を対話起動し、workspace trust承認が必要（`.mcp.json` はリポジトリに同梱されるため、`claude mcp list` は承認されるまで `⏸ Pending approval` を表示する。これはClaude Code自体の安全機構であり、本ADRの安全原則と一致するため回避しない）

### 呼び出し規約

- `cwd` は常に対象リポジトリのルートを明示指定する
- 読み取り専用の調査・要約・レビューには `sandbox: read-only` と `approval-policy: never` を使う
- ファイル変更を伴わせる場合でも `sandbox: workspace-write` かつ `approval-policy: on-request` 以上とし、`danger-full-access` および `approval-policy: never` の組み合わせでの書き込みタスクは使用しない
- Claude Codeの既存の承認ゲート（`CLAUDE.md` の Approval-gated operations）は、Codex経由の変更にも同様に適用する。Codexが提案した差分も、削除・大量上書き・外部送信等はユーザー承認前に確定しない

### 役割分担への影響

`docs/ai-routing.md` の初期役割分担（ChatGPT=要件/設計/レビュー、Claude Code=実装/ローカル操作）自体は変更しない。本ADRが変えるのは伝達経路のみで、人間によるコピー&ペーストのリレーを、Claude Code内の `codex` ツール呼び出しに置き換える。

## Consequences

### Positive

- 人間がChatGPTとClaude Codeの間で指示・結果を手動転記する工程をなくせる
- 同一ローカルディレクトリに対して2つの独立したコーディングエージェントが動くため、設計レビューと実装を同一セッション内で往復できる
- ChatGPT Plus/Proのログインで動作し、追加のAPI課金が発生しない
- 危険操作の承認ゲートは、既存のClaude Code権限プロンプトとCodexの `approval-policy` の二重で維持される

### Negative

- Phase 2で本来想定していた「人間リレーの手動ループを先に検証する」というroadmap-v0.1.mdの順序から外れる。ChatGPT自身は手動検証を先に行う意見だった旨、ユーザーから共有されている
- Codex側のセッション・スレッド状態はCodex CLI側のローカルストレージ（`~/.codex`）で管理され、`docs/orchestrator.md` のTask状態モデルとはまだ統合されていない
- ChatGPT/Codexアカウントの利用上限に達すると呼び出しが失敗する（本ADR作成時に実機確認で遭遇）

## Rejected alternatives

### 人間リレー（Phase 2の当初案）のみを継続する

安全だが、ユーザーが「自動化できる見込みがあれば手動検証を飛ばしてよい」と明示的に許可したため、技術検証が成功した時点で不採用とした。Phase 2 Issue #1 (LDL-001) 自体は、Orchestrator Core実装前の追跡可能性検証として引き続き有効であり、クローズはしない。

### 自前MCPサーバーでOpenAI APIを直接呼び出す

軽量だが、Codexエージェント自身が持つファイル操作・サンドボックス・承認フローを再実装することになり、Codex CLI純正のMCPサーバーモードより保守コストが高い。

### ChatGPT側（Codex Desktop/CLI）をハブにする

Claude CodeのMCPクライアント実装の方が本リポジトリでの運用実績・ドキュメントが厚く、`ai-routing.md` の役割分担（Claude Code=ローカル操作の主体）とも整合するため、Claude Codeをハブとする方を採用した。
