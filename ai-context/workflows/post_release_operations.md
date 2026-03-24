---
description: リリース後の運用統制を行う。エラー監視、インシデント対応、ロールバック判断、AIガバナンス監査の再確認を実施する。
---

# ワークフロー: /post-release-ops

## 目的
リリース後の安定運用を担保するため、監視・障害対応・ロールバック判断・監査対応を標準化する。

## Trigger
- `main` へリリース反映が完了した直後
- 重大障害の兆候を検知したとき
- AI関連インシデント（有害出力、法務懸念、説明責任問題）が発生したとき

## Step 0: AIガバナンス統制Workflowの自動発動（必須）
`ai-context/workflows/ai_governance_control.md` を実行し、リリース後の実運用リスクに対する監査を行う。

## Step 1: 監視開始確認
- エラー監視（例: Sentry）
- 可用性監視（例: 稼働率、レスポンスタイム）
- 事業KPI監視（例: 主要導線の成功率）

## Step 2: 初期観測ウィンドウ運用
- リリース後の重点監視時間帯を定義する（例: 1時間 / 24時間 / 72時間）。
- 重大アラートの一次対応者と連絡手段を明確化する。

## Step 3: インシデント対応
- 事象の切り分け（アプリ障害 / データ障害 / 外部API障害 / AI出力問題）。
- 影響範囲（ユーザー、データ、法務、収益）を評価。
- 一次緩和策を実施し、必要に応じて機能フラグで停止。

## Step 4: ロールバック判断
- ロールバック基準（停止時間、エラー率、法務リスク）を満たすか判定。
- 実施時は、対象バージョン、手順、復旧見込み、ユーザー告知方針を記録。

## Step 5: 再発防止と計画同期
- 原因分析（Root Cause）と再発防止策を定義。
- 実装計画書が存在する場合は進捗・課題・是正タスクを都度更新。
- 必要なら `release_gate` の基準を改訂して次回に反映。

## Step 6: 外部Skill導入の30日評価（該当時必須）
- 外部Skill/Plugin（例: 図解運用）を導入した場合、リリース後30日以内に運用評価を行う。
- 評価記録は固定パス `docs/operations/external_skill_30day_evaluations.md` に追記する（1評価1セクションで日付・対象Skill名を必ず記録）。
- 最低限の記録項目:
	- 図や成果物の作成回数（週）
	- 既存成果物の更新回数（週）
	- 1件あたり作業時間の中央値
	- レビュー指摘件数の推移
- 判定基準:
	- 継続: 作業時間または品質指標に明確な改善がある
	- 縮小: 一部のみ効果があり、運用対象を限定する
	- 停止: 運用負荷が高く再現性が低い

## 出力形式

### Post Release Ops Result
- Incident Level: Info / Minor / Major / Critical
- Current Status: Monitoring / Mitigating / Rolled Back / Resolved
- Governance Audit Result: PASS / CONDITIONAL / FAIL
- Rollback Decision: 実施 / 見送り（理由付き）
- External Skill 30-Day Review: 継続 / 縮小 / 停止（該当時）
- Next Actions
- Improvement Proposals（最低3件、Priority/Impact/Effort/Owner付き）
