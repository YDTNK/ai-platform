# AI Platform Reporting Policy

## 目的

AI Platformの設計・実装・運用に関する作業証跡を、チャット履歴に依存せずGitHub上で復元できるようにするための運用方針です。

このRepositoryでは、`engineering-career-hq` のdaily-log運用を参考にしつつ、AI Platformというプロダクトの開発履歴に適した形へ分離します。

## Repository間の責務

```text
YDTNK/00-ai-start-here
= GitHub全体の入口、Repository / Project / Source of Truthのルーティング

YDTNK/ai-platform
= AI Platformの設計・実装・運用・意思決定・プロダクト開発レポート

YDTNK/engineering-career-hq
= 学習・キャリア・個人進捗・実務経験・project daily-log

各実装Repository
= そのRepository固有のコード、CI/CD、デプロイ、Issue、PR、Commit
```

AI Platformの詳細な開発ログを `engineering-career-hq` へ重複保存しません。キャリア・学習・全体進捗へ影響する節目だけを、必要に応じて `engineering-career-hq/current-status.md` または該当Projectへ要約・参照リンクとして反映します。

## 保存構造

```text
reports/
├── daily/
│   ├── README.md
│   ├── YYYY-MM-DD.md
│   └── fragments/
│       └── YYYY-MM-DD/
│           └── NN-topic.md
└── templates/
    ├── daily-report-template.md
    └── fragment-template.md
```

## 2層記録

`engineering-career-hq` と同様に、作業中証跡と終了時レポートを分けます。

```text
1. fragment
   = 作業中の短いappend-only証跡

2. final daily report
   = その日の成果・判断・PR・課題・次回作業を整理した正本
```

### Fragment

保存先:

```text
reports/daily/fragments/YYYY-MM-DD/NN-topic.md
```

以下の単位で作成します。

- 1つの主要タスク
- 1つの大きなエラーまたは調査
- 1つの設計判断
- 1つのPRまたはRepository横断変更

1コマンド・1メッセージごとには作成しません。

### Final daily report

保存先:

```text
reports/daily/YYYY-MM-DD.md
```

その日の終了時、または重要な作業区切りで整備します。

## Source of Truth

| 情報 | 正本 |
|---|---|
| AI Platformの当日作業概要 | `reports/daily/YYYY-MM-DD.md` |
| 作業中の詳細証跡 | `reports/daily/fragments/YYYY-MM-DD/*.md` |
| 設計仕様 | `docs/` |
| 実装状態 | コード、テスト、Issue、PR、Commit |
| 現在のフェーズと優先順位 | `docs/roadmap-v0.1.md`、`docs/execution-plan-v0.1.md` |
| GitHub全体のRepositoryルーティング | `YDTNK/00-ai-start-here` |
| 学習・キャリア・個人進捗 | `YDTNK/engineering-career-hq` |

Daily reportは設計書やIssueの代替ではありません。詳細情報は正本へ残し、daily reportには要約と参照先を記録します。

## 作業開始時

必要な範囲で以下を確認します。

```text
1. README.md
2. CLAUDE.md
3. docs/roadmap-v0.1.md
4. docs/execution-plan-v0.1.md
5. 前回のreports/daily/YYYY-MM-DD.md
6. 当日のreports/daily/fragments/YYYY-MM-DD/
7. 対象Issue / PR /設計書
```

Repository横断作業では、先に `YDTNK/00-ai-start-here` のルーティングを確認します。

## 作業中

- 重要な実施内容、判断、エラー、検証結果をfragmentへ残す
- コードや設計変更は対象ファイル、Issue、PR、Commitへ残す
- 秘密情報、個人情報、トークン、APIキーを記録しない
- 未確認事項を完了扱いにしない

## 作業終了時

Final daily reportを整え、最低限以下を確認します。

- 実施内容が復元できるか
- 変更したRepository・ファイル・PRが記載されているか
- 判断理由と対象外が記載されているか
- テスト・確認結果が記載されているか
- 未対応事項と次回作業が明確か
- fragmentにしか存在しない重要情報がないか
- `engineering-career-hq` への節目同期が必要か

## engineering-career-hqへの同期条件

以下のいずれかに該当する場合だけ、要約と参照リンクを同期候補とします。

- AI Platformが学習計画またはキャリア戦略の正式プロジェクトになった
- 重要マイルストーンを完了した
- 新しい実務スキル・成果物として評価可能になった
- 全体ロードマップ、現在地、次の優先順位が変わった

日々の細かなcommit、エラー、Claude Codeの出力は重複保存しません。

## 禁止事項

- チャット履歴だけを根拠に後から日報を再構成する
- 1つのdaily reportへ作業中の全出力を追記し続ける
- 設計書、Issue、PR、Commitの内容を全文複製する
- `engineering-career-hq` と同じ詳細ログを二重管理する
- 未実施のテスト、push、mergeを実施済みと記録する
- 秘密情報や個人情報を記録する

## 命名

- Final daily report: `YYYY-MM-DD.md`
- Fragment: `NN-topic.md`
- 文書本文: 日本語
- 技術用語・ファイル名・Commit message: 既存ルールに従う
