---
name: skill-authoring-quality
description: Rules・Workflows・Skillsを新規作成または改修する時に使用する。要件曖昧性の解消、設計パターン選定、品質レビュー、反復改善を一体で実行し、長期運用しやすい記述品質に仕上げる。
---

# Goal

カスタマイズ文書の品質を一定水準に保ち、将来の保守負荷を下げる。

# When to Use

- 新しいSkillやWorkflowを追加する。
- 既存文書の肥大化や重複を整理する。
- うまく発動しない説明文を改善する。

# When NOT to Use

- 単発のコード修正のみを行う時。
- 文書変更を伴わない障害対応を行う時。

# Trigger Templates

以下の依頼文をそのまま使うと、文書品質改善の適用対象が明確になる。

- 「このSkillのdescriptionを発動しやすい表現に直して」
- 「Workflowの重複を減らして長期運用しやすく再設計して」
- 「新規Skillを作るので、要件質問から品質レビューまで回して」
- 「rules/workflows/skills の責務が崩れていないかレビューして」
- 「この文書を Safety Gate パターンで書き直して」

# Step 1: Clarify First

要件が曖昧な場合は、実装前に必須質問を最小数で行う。

- 目的は何か。
- 適用範囲はどこか。
- 成功条件は何か。
- 禁止事項は何か。
- 正本はどこか。

# Step 2: Choose Pattern

対象に応じて構造パターンを選ぶ。

- Routing: 複数タスクを振り分ける時。
- Sequential Pipeline: 前段結果を後段で使う時。
- Linear Progression: 常に同じ手順で進める時。
- Safety Gate: 破壊的操作前に確認が必要な時。
- Task-Driven: 依存関係のある複雑タスクを追跡する時。

# Step 3: Authoring Rules

- description は発動トリガー語を具体的に含める。
- 文書は短く保ち、詳細は必要時に参照させる。
- 役割分離を崩さない（rules / workflows / skills）。
- Why中心で記述し、単なる操作説明の羅列を避ける。

# Step 4: Review Loop

1. 初稿を作る。
2. 構造・安全・運用観点で自己レビューする。
3. 問題を修正する。
4. 収束するまで反復する。

# Quality Checklist

- 目的と適用範囲が明確。
- When to Use / When NOT to Use がある。
- 出力形式が定義されている。
- 参照導線が正本に向いている。
- 重複定義を避けている。

# 出力形式

## 文書品質レビュー結果
- 対象範囲
- 選択したパターン
- 品質指摘
- 反映した修正
- 残余リスク
