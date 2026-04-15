---
name: git-commit-formatter
description: ユーザーが「コミットして」「変更を保存して」と依頼した際や、一連のタスクが完了してGitにコミットする必要がある場合に自動で実行します。Conventional Commitsのルールに従い、高品質なコミットメッセージを生成します。
---

# Goal
Gitのコミットメッセージを、Conventional Commitsの仕様に沿って生成・提案すること。

# Instructions
1. `git status` および `git diff` を実行し、直近の変更内容を正確に把握してください。
2. 以下のフォーマットに従ってコミットメッセージを作成してください。
   `<type>[optional scope]: <description>`
   
   `[optional body]`

3. **Constraints (禁止事項):**
   - `update`、`fix`、`変更`、`対応` といった曖昧で具体性のない表現は絶対に使用しないでください。
   - メッセージのdescriptionやbodyの出力は、必ず日本語で行ってください（typeとscopeは英語で構いません）。

# Examples (出力例)
`feat(auth): ログイン時のバリデーション処理を追加`
`fix(api): ユーザーデータ取得時のNullPointerExceptionを修正`
`docs: READMEに環境構築の手順を追記`
