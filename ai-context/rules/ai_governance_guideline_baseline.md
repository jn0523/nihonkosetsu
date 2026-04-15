# AI Governance Guideline Baseline

最終更新日: 2026-03-24
目的: AIガバナンスWorkflowが参照する外部ガイドラインの既知ベースラインを管理する。

## 1) 総務省・経産省 AI事業者ガイドライン
- 掲載ページ: https://www.soumu.go.jp/main_sosiki/kenkyu/ai_network/02ryutsu20_04000019.html
- 既知最新: 第1.1版（令和7年3月28日 公表）
- 既知履歴: 第1.01版（令和6年11月22日）/ 第1.0版（令和6年4月19日）
- 実務上の観点:
  - 事業者向けの統一的ガイドラインとして運用
  - 本編・別添・見え消し版が提供され、改訂差分の追跡が可能

## 2) Anthropic Constitution
- URL: https://www.anthropic.com/constitution
- 既知内容:
  - 優先順位: Broadly safe > Broadly ethical > Anthropic guidelines > Genuinely helpful
  - ハード制約（hard constraints）と広義の安全性・倫理・有用性の統合
  - 継続改訂される「living framework」であることを明示

## 3) Anthropic Responsible Scaling Policy
- URL: https://www.anthropic.com/news/announcing-our-updated-responsible-scaling-policy
- 既知更新: 2024-10-15 更新版
- 既知内容:
  - Capability Thresholds と Required Safeguards
  - ASLベースの段階的安全措置
  - 評価・文書化・外部入力を含む実装ガバナンス

## 4) NIST AI RMF
- URL: https://www.nist.gov/itl/ai-risk-management-framework
- 既知内容:
  - AI RMF 1.0（2023-01-26）
  - Generative AI Profile（NIST AI 600-1, 2024-07-26）
  - Govern / Map / Measure / Manage の実務運用

## 5) OECD AI Principles
- URL: https://oecd.ai/en/ai-principles
- 既知内容:
  - 2019採択、2024年5月更新
  - 信頼できるAIの価値原則（人権、公平性、透明性、堅牢性、説明責任）

## Diff Update Rule
- Workflow実行時は、上記URLを再確認し、版数・更新日・主要原則を比較する。
- 差分を検知した場合は:
  - 差分内容を企画書/実装計画書/リリース判定に反映
  - このベースラインを更新対象として記録
