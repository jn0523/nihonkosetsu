---
description: 最終リリース前のGo/No-Go判定を実施する。実行時にAIガバナンス統制Workflowを自動発動し、最新ガイドライン差分と法務・倫理・安全性の適合を確認する。
---

# ワークフロー: /release-gate

## 目的
本番リリース前に、品質・セキュリティ・法務・AI倫理・ガバナンスの観点で最終判定（Go / Conditional Go / No-Go）を行う。

## Step 0: AIガバナンス統制Workflowの自動発動（必須）
最終リリース判定時は、`ai-context/workflows/ai_governance_control.md` を必ず自動実行する。

- 参照元ガイドラインを都度確認する。
- `ai-context/rules/ai_governance_guideline_baseline.md` と比較し、差分があれば反映する。
- `FAIL` 判定が含まれる場合はリリース不可。

## Step 1: リリース対象の確認
- リリース範囲（機能、バージョン、対象環境）を明確化する。
- 変更差分と影響範囲（ユーザー、データ、外部連携）を整理する。

## Step 2: 品質・運用確認
- テスト結果（ユニット、結合、E2E）
- 監視・アラート設定
- 障害時ロールバック手順

## Step 3: 法務・倫理・セキュリティ最終確認
- 利用規約、プライバシーポリシー、権利処理、外部API規約
- AI利用時の説明責任、苦情対応、監査ログ
- 機密情報管理、権限制御、脆弱性対応

## Step 4: 最終判定
- Go: 主要リスクが許容範囲で、未対応の重大課題なし
- Conditional Go: 残課題が軽微で期限・責任者付きで是正可能
- No-Go: 重大リスク未解消、またはAIガバナンス統制でFAIL

## 出力形式

### Release Gate Result
- Verdict: Go / Conditional Go / No-Go
- Blocking Issues
- Required Actions Before Release
- Post-Release Monitoring Plan
- Governance Diff Reflection（ガイドライン差分反映結果）
