# Role Skills Index

このディレクトリは、複数ロールで計画・実行するための役割定義カードを管理します。

## 設計ポリシー
- エディタ非依存の資産は ai-context 配下に集約する。
- 各ロール定義は短く、明示的で、再利用可能に保つ。
- 可能な限り既存の正本Workflowへ接続する。
- ロール間の引き継ぎ条件を明確に記述する。

## ロール一覧
- product-planner: 課題定義、要件形成、スコープ判断。
- tech-architect: 技術実現性、構成設計、実装計画。
- ui-designer: 画面構成、体験品質、操作一貫性。
- governance-reviewer: AIガバナンス、法務・倫理・安全統制。
- platform-compliance-checker: App Store / Google Play 規約適合確認。
- release-gatekeeper: 最終リリース判定（PASS/CONDITIONAL/FAIL）。
- post-release-operator: リリース後監視、障害対応、再発防止。
- marketing-operator: 集客施策、訴求設計、チャネル運用。
- finance-risk-controller: 収支防衛、下方リスク、閾値管理。

## 共通スキル（外部知見の内製化）
- config-health-auditor: 設定劣化を健診し、Critical/Structural/Incrementalで改善提案を出す。
- diagram-iteration-operator: 図解を単発生成で終わらせず、反復更新とJSON保存で運用する。
- skill-authoring-quality: 要件明確化、設計パターン選定、品質レビュー反復で文書品質を保つ。

## 推奨実行順
1. product-planner
2. tech-architect と ui-designer
3. governance-reviewer と platform-compliance-checker
4. marketing-operator と finance-risk-controller
5. release-gatekeeper
6. post-release-operator
