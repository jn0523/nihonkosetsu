---
description: releaseブランチからmainブランチへ反映する直前のリリース前検査を実施する。実行時にAIガバナンス統制、アプリプラットフォーム規約チェック、Cloudflare型セキュリティ監査を自動発動し、最新ガイドライン差分、法務・倫理・安全性、ストア審査適合、および脆弱性の確定検査を確認する。
---

# ワークフロー: /release-gate

## 目的
本ワークフローは、**`release/*` から `main` へ push / merge する直前のみ** に限定して適用する。通常の開発作業（`feature/*` や `bugfix/*` のコミット、PR作成など）では実行しない。
品質・セキュリティ・法務・AI倫理・ガバナンスの観点で最終判定（PASS / CONDITIONAL / FAIL）を行う。

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

## Step 0.6: Cloudflare型セキュリティ監査Workflowの自動発動（必須）
最終リリース判定時は、`ai-context/workflows/security_audit.md` を必ず自動実行する。

1. **Skillのインストール確認・実行:**
   - 未導入の場合は、公式Skills CLIを用いてスキルをインストールする。
     ```bash
     npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit --global
     ```
   - Node.js 環境（バリデータスクリプト実行用）が存在することを確認する。
2. **監査スキルの稼働 (Full Audit Mode):**
   - リリース差分スコープ（`scoped`）または全体（`standard`）を指定し、Readme準拠の稼働構文で実行する。
     ```text
     # 差分スコープ監査（推奨）
     do a security review with profile: scoped, output to ~/security-audit-skill/<repo-name>/run-1

     # 全体監査
     security audit this codebase, output to ~/security-audit-skill/<repo-name>/run-1
     ```
3. **判定条件:**
   - `confirmed` な脆弱性（Critical / High / Medium）が **0件** であること。
   - `confirmed` の Low 指摘のみ存在する場合は、リリース後直ちに対応する是正計画（期日・担当者付き）を提示できれば CONDITIONAL 許容。
   - `needs_validation` の未解決項目に、リリースを阻害する重大な未検証リスクが存在しないこと。
   - `FAIL` 判定（未解決のConfirmed重大脆弱性、または致命的なneeds_validation残存）が含まれる場合はリリース不可。

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
- **実機QAチェックリストの全項目PASS確認（必須）:**
  - `ai-context/rules/app_validation_policy.md` に準拠した最終テストチェックリスト（`planning/` または `docs/qa/` 配下）が実施記録付きで存在すること。
  - **ダークモード表示・視認性:** OS/アプリ内切替即時反映、コントラスト比確保、ダイアログ白飛び/黒潰れ、横画面・文字拡大対応がPASSしていること。
  - **課金ライフサイクル・失効後挙動:** 有料プラン失効後の有料オプション遮断、Free上限超過データの保全（閲覧可）と新規作成ブロック、ソフトペイウォール再表示が検証済みであること。
  - **AI機能・耐障害性:** 入力境界値、プラン別回数制限、エラー通知（429/クォータ/5xx）、タイムアウト・キャンセル、不正JSONフォールバックが検証済みであること。
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
- PASS: 主要リスクが許容範囲で、未対応の重大課題なし（セキュリティ監査でConfirmed重大脆弱性0件）
- CONDITIONAL: 残課題が軽微で期限・責任者付きで是正可能（セキュリティ監査でConfirmed Lowのみ、かつ是正計画あり）
- FAIL: 重大リスク未解消、またはAIガバナンス統制/プラットフォーム規約チェック/セキュリティ監査でFAIL、または損害賠償を含む下方リスクが収支防衛ラインを超過

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

## セキュリティ監査結果（Cloudflare Security Audit）
- 判定: PASS / CONDITIONAL / FAIL
- 適用プロファイル: quick / standard / deep / scoped
- カバレッジ達成状況:
- Confirmed 脆弱性（件数/詳細）:
  - Critical: 0件
  - High: 0件
  - Medium: 0件
  - Low: 0件
- Needs Validation（未解決リスク）:
- 証跡（findings.json / coverage-ledger.json）:

## 外部Skill統制チェック
- AGENTS 外部Skill導入ルール: PASS / CONDITIONAL / FAIL / N/A
- localhost 境界（diagram ops）: PASS / CONDITIONAL / FAIL / N/A
- 30日評価記録（docs/operations/external_skill_30day_evaluations.md）: PASS / CONDITIONAL / FAIL / N/A
- ライセンス対応（再記述・出典明示）: PASS / CONDITIONAL / FAIL / N/A
- 証跡:

## 実機QA検証チェック
- チェックリスト配置パス:
- ダークモード・表示整合性: PASS / CONDITIONAL / FAIL
- 課金ライフサイクル・失効後制御: PASS / CONDITIONAL / FAIL / N/A
- AI機能・耐障害性: PASS / CONDITIONAL / FAIL / N/A
- 未解決の重大不具合: なし / あり（内容: ）
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
- Security Audit Findings（Confirmed件数、Needs Validation状況、合否判定）
- External Skill Control Check（ライセンス、公開範囲、運用記録の適合状況）
- Liability Exposure Assessment（想定最大損失、発生確率、防衛ライン比較、残余リスク）
- Financial Guardrail Decision（防衛ライン内 / 要縮退 / 要延期）
- Operational Governance Snapshot（RACI、エスカレーション経路、承認フロー）
- SLA/SLO Readiness（一次応答、復旧目標、告知期限、補償基準の充足状況）
- Improvement Proposals（最低3件、Priority/Impact/Effort/Owner付き）
