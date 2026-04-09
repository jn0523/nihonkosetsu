---
name: config-health-auditor
description: AIエディタの設定が効かない、ルールが無視される、コンテキストが重い、運用が不安定、といった症状がある時に設定健診を実施する。AGENTS.md、入口ファイル、ai-context配下の整合を監査し、優先度付きで改善案を提示する。
---

# Goal

AIエディタ設定の劣化を早期検知し、運用品質を維持する。

# When to Use

- ルールが守られない、もしくは反映が不安定。
- 同じ指摘を何度も繰り返している。
- 起動時のコンテキストが重く応答品質が不安定。
- 外部Skill導入後に動作が変わった。

# When NOT to Use

- 単一機能の実装相談のみをしたい時。
- ランタイム障害の直接原因調査のみをしたい時。

# Trigger Templates

以下の依頼文をそのまま使うと、起動意図が明確になる。

- 「設定健診をして。ルールが効いていない箇所を洗い出して」
- 「さっきも言ったよね」
- 「AGENTS.md と workflows/skills/rules の整合を監査して」
- 「最近の運用ドリフトを Critical / Structural / Incremental で分類して」
- 「外部Skill導入後の設定劣化チェックを実施して」
- 「リリース前に設定健診レポートを作成して」

# Audit Scope

- 正本構成: AGENTS.md を起点に、入口ファイルが薄い構成を維持できているか。
- ルール配置: rules / workflows / skills の責務分離が崩れていないか。
- 設定過積載: 常時読み込み対象が肥大化していないか。
- セキュリティ: 機密情報の露出経路、危険な許可、外部公開リスク。
- 外部Skill統制: 再記述原則、ライセンス配慮、30日評価運用。

# Procedure

1. 現在の構成を棚卸しし、正本との不整合を抽出する。
2. 問題を Critical / Structural / Incremental の3段階で分類する。
3. 修正候補を、影響範囲と工数を添えて提案する。
4. 自動修正は行わず、合意後に反映する。

# Checkpoints

- Critical:
  - セキュリティ原則違反（認証なしAPIの公開、機密情報露出）。
  - 正本衝突（複数ファイルに同種ルールが重複し矛盾）。
- Structural:
  - 役割境界の崩れ（rules/workflows/skillsの混在）。
  - 入口ファイルの肥大化。
- Incremental:
  - 参照導線の不足。
  - 表現ゆれやメンテナンス性の低下。

# 出力形式

## 設定健診結果
- 判定: PASS / CONDITIONAL / FAIL
- Critical 指摘
- Structural 指摘
- Incremental 指摘
- 改善提案（Priority / Impact / Effort / Owner）

# Non-goals

- 合意なしの一括自動修正。
- 実装コードの振る舞い変更。
