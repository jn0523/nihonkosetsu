---
description: releaseブランチからmainブランチへ反映する直前のリリース前検査を実施する。実行時にAIガバナンス統制とアプリプラットフォーム規約チェックを自動発動し、最新ガイドライン差分と法務・倫理・安全性・ストア審査適合を確認する。
---

# ワークフロー: /release-gate

## 目的
`release/*` から `main` へ push / merge する直前に、品質・セキュリティ・法務・AI倫理・ガバナンスの観点で最終判定（PASS / CONDITIONAL / FAIL）を行う。

## Step 0: AIガバナンス統制Workflowの自動発動（必須）
最終リリース判定時は、`ai-context/workflows/ai_governance_control.md` を必ず自動実行する。

- 参照元ガイドラインを都度確認する。
- `ai-context/rules/ai_governance_guideline_baseline.md` と比較し、差分があれば反映する。
- `FAIL` 判定が含まれる場合はリリース不可。

## Step 0.5: アプリプラットフォーム規約チェックWorkflowの自動発動（必須）
最終リリース判定時は、`ai-context/workflows/platform_compliance_gate.md` を必ず自動実行する。

- 本チェックはAIガバナンス統制とは別機能として扱う。
- Apple App Store / Google Play の審査基準・配布契約・課金/メタデータ要件を確認する。
- `ai-context/rules/app_platform_policy_baseline.md` と比較し、差分があれば反映する。
- `FAIL` 判定が含まれる場合はリリース不可。

## Step 0.7: 外部Skill導入統制の確認（必須）
最終リリース判定時は、外部Skill/Plugin由来の運用が含まれる場合に、以下を必ず確認する。

- `AGENTS.md` の「外部Skill導入ルール」に適合していること。
- 図解運用を行う場合は `ai-context/workflows/diagram_ops_with_mcp_excalidraw.md` のセキュリティ原則（localhost運用、非公開、機密データ非投入）を満たしていること。
- 30日試行を実施中または実施済みの場合は、`ai-context/workflows/post_release_operations.md` の外部Skill評価手順に従い、固定パス `docs/operations/external_skill_30day_evaluations.md` に記録が存在すること。
- 設定ドリフトが疑われる場合は `ai-context/skills/config-health-auditor.md` の手順で健診し、判定結果を添付すること。

## Step 1: ブランチ前提条件の確認（必須）
- 現在の反映元ブランチが `release/*` であることを確認する。
- 反映先が `main` であることを確認する。
- 反映経路（PR / merge / push）と承認者を明確化する。

## Step 2: リリース対象の確認
- リリース範囲（機能、バージョン、対象環境）を明確化する。
- 変更差分と影響範囲（ユーザー、データ、外部連携）を整理する。

## Step 3: 品質・運用確認
- テスト結果（ユニット、結合、E2E）
- 監視・アラート設定の有効化確認
- 障害時ロールバック手順の実行可能性確認

## Step 4: 法務・倫理・セキュリティ最終確認
- 利用規約、プライバシーポリシー、権利処理、外部API規約
- AI利用時の説明責任、苦情対応、監査ログ
- 機密情報管理、権限制御、脆弱性対応
- 損害賠償請求リスク（利用者/第三者被害シナリオ、想定最大損失、初動対応費、法務対応費、返金/補償費）の再評価
- 収支防衛ライン（想定利益・許容損失上限）を超える下方リスクの有無確認
- 運用責任分界（RACI）の最終確認（重大障害時の意思決定者、承認者、実行者、連絡順序）
- SLA/SLO準備状況の確認（一次応答時間、復旧目標、告知期限、返金/補償判断基準）

## Step 5: 最終判定
- PASS: 主要リスクが許容範囲で、未対応の重大課題なし
- CONDITIONAL: 残課題が軽微で期限・責任者付きで是正可能
- FAIL: 重大リスク未解消、またはAIガバナンス統制/プラットフォーム規約チェックでFAIL、または損害賠償を含む下方リスクが収支防衛ラインを超過

## Step 6: リリース後運用Workflowへの引き継ぎ
- 反映後は `ai-context/workflows/post_release_operations.md` を実行し、監視・障害対応・ロールバック判断を運用する。

## 出力形式

### 提出物テンプレート（健診結果フォーマット）

以下をリリース判定の提出物として記録する。

```markdown
# リリースゲート提出物

## 基本情報
- 日付:
- リリースブランチ:
- 反映先:
- レビュー担当:
- 対象範囲:

## 最終判定
- 判定: PASS / CONDITIONAL / FAIL
- 判定サマリー:

## 設定健診結果
- 判定: PASS / CONDITIONAL / FAIL
- Critical 指摘:
- Structural 指摘:
- Incremental 指摘:
- 証跡:

## 外部Skill統制チェック
- AGENTS 外部Skill導入ルール: PASS / CONDITIONAL / FAIL / N/A
- localhost 境界（diagram ops）: PASS / CONDITIONAL / FAIL / N/A
- 30日評価記録（docs/operations/external_skill_30day_evaluations.md）: PASS / CONDITIONAL / FAIL / N/A
- ライセンス対応（再記述・出典明示）: PASS / CONDITIONAL / FAIL / N/A
- 証跡:

## ガバナンス / プラットフォーム
- ガイドライン差分反映:
- ストアポリシー差分反映:
- ブロッカー:

## リスク / 運用
- 損害賠償リスク評価:
- 収支防衛ライン判定:
- 運用統制スナップショット:
- SLA/SLO 準備状況:

## リリース前必須対応
- リリース前に必ず直す項目:
- 担当者 / 期限:

## 改善提案
- Priority / Impact / Effort / Owner:
```

### リリースゲート結果
- 判定: PASS / CONDITIONAL / FAIL
- Blocking Issues
- Required Actions Before Release
- Governance Diff Reflection（ガイドライン差分反映結果）
- Platform Policy Diff Reflection（ストアポリシー差分反映結果）
- External Skill Control Check（ライセンス、公開範囲、運用記録の適合状況）
- Liability Exposure Assessment（想定最大損失、発生確率、防衛ライン比較、残余リスク）
- Financial Guardrail Decision（防衛ライン内 / 要縮退 / 要延期）
- Operational Governance Snapshot（RACI、エスカレーション経路、承認フロー）
- SLA/SLO Readiness（一次応答、復旧目標、告知期限、補償基準の充足状況）
- Improvement Proposals（最低3件、Priority/Impact/Effort/Owner付き）
