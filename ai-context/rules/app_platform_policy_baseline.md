# App Platform Policy Baseline

最終更新日: 2026-03-25
目的: Apple App Store / Google Play のプラットフォーム規約チェックWorkflowが参照する既知ベースラインを管理する。

## 1) Apple App Review Guidelines
- URL: https://developer.apple.com/app-store/review/guidelines/
- 既知最新: Last Updated February 6, 2026
- 既知観点:
  - Safety / Performance / Business / Design / Legal の5分類
  - IAP原則、メタデータ正確性、審査情報の完全性、プライバシー/権限説明責任
  - 申請時のデモアカウント/レビュー用情報提供の要件

## 2) Apple Developer Program Terms
- URL: https://developer.apple.com/support/terms/
- 既知観点:
  - Apple Developer Program License Agreement を含む契約群
  - データ取り扱い、配布、コンプライアンス、アカウント運用の拘束条件

## 3) Google Play Developer Policy Center
- URL: https://play.google/developer-content-policy/
- 既知観点:
  - 制限コンテンツ、ユーザーデータ、権限、SDK責任、収益化、ストア掲載情報、スパム/マルウェア、ファミリー向け
  - ポリシー更新情報（PolicyBytes）と最近の更新導線

## 4) Google Play Developer Distribution Agreement
- URL: https://play.google.com/intl/en_us/about/developer-distribution-agreement.html
- 既知観点:
  - 配信契約上の義務、ポリシー準拠、違反時措置
  - 継続提供と責任分界の前提条件

## Diff Update Rule
- Workflow実行時は上記URLを再確認し、更新日・主要変更点・影響範囲を比較する。
- 差分を検知した場合は:
  - 企画書/リリース判定へ差分要求を反映
  - このベースラインを更新対象として記録
