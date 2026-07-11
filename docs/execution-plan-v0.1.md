# AI Platform Execution Plan v0.1

## 1. 目的

本書はRoadmapのPhaseを、担当・入力・出力・依存関係・検証方法・完了条件を持つ実行可能なTaskへ分解する。

現在地の正本はengineering-career-hq/projects/ai-platform/progress.mdとし、本書は実行順序とTask定義を扱う。

## 2. Task階層

~~~text
Epic
└── Milestone
    └── Issue
        └── Task checklist
~~~

- Epic: Phase全体の目的と成功条件
- Milestone: GitHub上のPhase進捗集約
- Issue: 原則1つのPull Requestで完了できる単位
- Task: Issue本文内の実行チェックリスト

## 3. 共通Task項目

- Task ID
- 対応Requirement
- 目的
- 入力
- 担当
- 依存関係
- 対象範囲
- 対象外
- 実施内容
- 出力
- 検証方法
- 完了条件
- Risk level
- 承認要否
- 管理側同期要否

## 4. Phase 1: Foundation

### FND-000 GitHub共通言語ポリシー

Status: COMPLETED

- 共通ポリシー正本を00-ai-start-hereへ作成
- AI Platform固有ルールをCLAUDE.mdとdevelopment-rulesへ反映
- PRと管理側同期を完了

### FND-001 Foundation文書・構成監査

Status: IN_PROGRESS

対応要件: FR-001〜FR-007、NFR-001〜NFR-006

出力:

- requirements-v0.1
- 最適化されたREADME
- 整合したArchitecture、Roadmap、Execution Plan、Runbook
- gitignore
- Issue・PRテンプレート
- Claude Code必読順の更新

完了条件:

- 文書の優先順位が明記される
- 最上位要件から完成まで追跡できる
- 古い次タスク・完了状態が修正される
- GitHub安全基盤とテンプレートが存在する
- Pull Requestでレビューされmainへ反映される

### FND-002 Phase管理構造登録

Status: NOT_STARTED

依存: FND-001

実施内容:

1. Phase 1 EpicをIssueとして作成
2. Phase 1 Milestoneを作成
3. Foundation IssueをMilestoneへ関連付け
4. 各IssueにTask checklistと完了条件を設定
5. 既存Issue #1をPhase 2統合検証Issueとして位置付ける

完了条件:

- Epic → Milestone → Issue → TaskをGitHubで追跡できる
- Issue本文は日本語、タイトルは英語になっている
- 重複Issueを作成していない

### FND-003 Claude Code初回調査

Status: NOT_STARTED

依存: FND-001

実施内容:

1. ローカルcloneをmainへ更新
2. Claude CodeをRepository直下で起動
3. 必読文書を読み込ませる
4. ファイル変更なしで目的、要件、制約、矛盾、計画を報告させる
5. 承認前に変更がないことを確認

完了条件:

- Claude CodeがSource of Truth、要件、Phase、承認条件を説明できる
- git statusに意図しない変更がない
- 調査結果が管理側fragmentへ保存される

### FND-004 Phase 1完了監査

Status: NOT_STARTED

依存: FND-002、FND-003

確認項目:

- 要件・設計・Roadmap・Execution Plan・Runbook整合
- gitignore・Issueテンプレート・PRテンプレート存在
- 秘密情報不存在
- 最初のIssue存在
- Claude Code初回調査成功
- 管理側Source of Truth同期

完了条件:

- Phase 1をCOMPLETEDまたはBLOCKEDとして客観的に判定できる
- Roadmapと管理側progressへ結果が反映される

## 5. Phase 2: Local Development Loop

### LDL-001 ローカル同期

~~~bash
git switch main
git pull --ff-only origin main
git status
~~~

### LDL-002 Issue #1事前調査

- Issue #1と関連設計を読む
- 変更なしで計画、対象、リスク、検証方法を提示する
- 人間の承認を得る

### LDL-003 Feature Branch

~~~bash
git switch -c feature/local-development-loop
~~~

実際のIssue内容に合わせて英語kebab-caseのBranch名へ変更できる。

### LDL-004 実装・検証

- 承認された最小変更だけを実装
- 実在するTest、Lint、Format、Buildだけを実行
- git status、git diff --stat、git diffを確認
- 秘密情報が含まれないことを確認

### LDL-005 Commit・Push・Pull Request

~~~bash
git add <approved-files>
git diff --cached --stat
git diff --cached
git commit -m "<type>: <summary>"
git push -u origin HEAD
~~~

PRにはIssue、Requirement、変更内容、検証結果、Risk、未解決事項、管理側同期要否を記録する。

### LDL-006 Review・Merge・同期

1. ChatGPTまたは人間が差分をレビュー
2. 必要な修正を実施
3. mainへマージ
4. fragment、daily-log、progress、review-taskを同期
5. current-status更新要否を判断
6. Issue #1を完了条件に基づきClose

## 6. Phase 3以降の共通実行順序

1. Requirements更新
2. Architecture・関連仕様更新
3. Roadmap・Execution Plan更新
4. ADRまたはDecision記録
5. Issue作成
6. Claude Code事前調査
7. 実装計画
8. 人間承認
9. Branch作成
10. 実装
11. Test・Security確認
12. Pull Request
13. Review
14. Merge
15. 管理側同期
16. Requirementと完了条件の受入確認

## 7. Phase 1残タスク

- [x] README
- [x] Architecture
- [x] Development Rules
- [x] Roadmap
- [x] Execution Plan
- [x] Runbook
- [x] AI Routing
- [x] Orchestrator
- [x] Workspace
- [x] Claude Code初期化プロンプト
- [x] 最初のIssue
- [x] GitHub共通言語ポリシー
- [ ] Requirements
- [ ] gitignore
- [ ] Issueテンプレート
- [ ] Pull Requestテンプレート
- [ ] 文書間整合レビューとmain反映
- [ ] Epic・Milestone・Issue・Task登録
- [ ] Claude Code初回調査
- [ ] Phase 1完了監査

本Pull Requestで追加・反映する項目は、mainへのマージ後にCOMPLETEDへ更新する。実行中のBranch上だけで完了扱いにしない。

## 8. Phase 1完了定義

- 最上位要件からIssue・実装まで追跡できる
- 主要文書の役割・優先順位・現在地が一致する
- Claude Codeの初期化と初回調査が再現可能
- 秘密情報の誤コミット対策がある
- Issue・PR標準形式がある
- Phase管理構造と最初のIssueが登録されている
- 管理側Source of Truthへ結果が同期されている
