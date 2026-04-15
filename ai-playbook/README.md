# Cursor・Claude Code・Antigravity・GitHub Copilot 汎用設定・ベストプラクティス

Cursor・Claude Code（Ckaudecode）・Antigravity（Google Gemini IDE）・GitHub Copilot を実務で効率的に活用するための設定術、ベストプラクティス、および汎用プロンプト（`GEMINI.md` / `CLAUDE.md` / `AGENTS.md` / `copilot-instructions.md`）の設計パターンを体系的にまとめました。

このリポジトリは、Cursor・Claude Code・Antigravity・GitHub Copilot でのパフォーマンスを高めるために、Skills・Rules・Workflows を横断的に設計・管理することを目的としています。

---

## 1. ツール別の適用方法（Rules / Workflows / Skills の配置先）

まず最初に、4ツールそれぞれで「どこに置けば、どう効くか」を一覧化します。

### 1-0. エディタ非依存で運用する共通方針
* **共通資産の配置先:** ツール固有名ではなく、`プロジェクトルート/ai-context/` に集約する。
* **フォルダ責務を固定化:**
  * `ai-context/rules/`: 行動規範・制約（常時参照）
  * `ai-context/workflows/`: 手動実行の手順書（計画・実行フロー）
  * `ai-context/skills/`: 自動発動を想定した専門知識
* **入口ファイルを薄く保つ:** `GEMINI.md` / `CLAUDE.md` / `.github/copilot-instructions.md` / `.cursorrules` は、原則として `AGENTS.md` を参照するだけにする。

### 1-1. Cursor
* **Rules（常時ルール）**
   * 推奨: `プロジェクトルート/.cursorrules`
   * 共通化: `.cursorrules` から `AGENTS.md` 参照方針を明記
* **Workflows（再利用プロンプト）**
   * 推奨: Cursor 側の再利用プロンプト機能に `ai-context/workflows/` の内容を移植して利用
* **Skills（専門知識の拡張）**
   * CursorのProject RulesやDocs機能から `ai-context/skills/` を参照する運用に統一

### 1-2. Antigravity（Gemini IDE）
* **Rules（常時ルール）**
   * グローバル: `~/.gemini/GEMINI.md`
   * ワークスペース: `プロジェクトルート/ai-context/rules/*.md`
* **Workflows（手動起動手順）**
   * グローバル: `~/.gemini/antigravity/global_workflows/*.md`
   * ワークスペース: `プロジェクトルート/ai-context/workflows/*.md`
* **Skills（自動発動の専門知識）**
   * グローバル: `~/.gemini/antigravity/skills/`
   * ワークスペース: `プロジェクトルート/ai-context/skills/`
* **補足**
   * WindowsのグローバルRulesは `C:\Users\ユーザー名\.gemini\GEMINI.md` です。

### 1-3. Claude Code
* **Rules（常時ルール）**
   * 推奨: `プロジェクトルート/CLAUDE.md`
   * 共通化: `CLAUDE.md` から `AGENTS.md` を参照するSSoT構成
* **Workflows（手動起動手順）**
   * 推奨: `プロジェクトルート/.claude/commands/*.md`
   * 使い方: スラッシュコマンドで呼び出す（例: `/plan`）
* **Skills（専門知識の拡張）**
   * Claude CodeにはAntigravityのような「Skills」専用ディレクトリが標準化されていないため、
   `AGENTS.md` または `ai-context/skills/*.md` を参照する形で運用する
   * 実務上は「Rules + Commands + 共通知識ファイル」の3点で代替する

### 1-4. GitHub Copilot（VS Code）
* **Rules（常時ルール）**
   * 推奨: `プロジェクトルート/.github/copilot-instructions.md`
   * 共通化: `copilot-instructions.md` から `AGENTS.md` を参照するSSoT構成
* **Workflows（再利用プロンプト）**
   * 推奨: `プロジェクトルート/.github/prompts/*.prompt.md`
   * 既存運用がある場合: `プロジェクトルート/copilot-custom-prompts/` を運用ディレクトリとして利用
* **Skills（専門知識の拡張）**
   * Copilotでもプロジェクト固有の知識は `プロジェクトルート/ai-context/skills/*.md` に置き、
      `copilot-instructions.md` 側から参照ルールを明記して利用する

### 1-5. このリポジトリでの推奨配置
* **共通ルール（SSoT）:** `AGENTS.md`
* **Claude Code入口:** `CLAUDE.md`（`AGENTS.md` を参照）
* **Antigravity入口:** `GEMINI.md`（`AGENTS.md` を参照）
* **Cursor入口:** `.cursorrules`（`AGENTS.md` 参照方針を明記）
* **Copilot入口:** `.github/copilot-instructions.md`（`AGENTS.md` を参照）
* **Rules共通格納:** `ai-context/rules/`
* **Skills共通格納:** `ai-context/skills/`
* **Workflows共通格納:** `ai-context/workflows/`

```text
.
├─ AGENTS.md
├─ CLAUDE.md
├─ GEMINI.md
├─ .cursorrules
├─ .github/
│  └─ copilot-instructions.md
└─ ai-context/
   ├─ rules/
   ├─ workflows/
   └─ skills/
```

### 1-6. フォルダ構成の意図
* **変更耐性の確保:** エディタ固有フォルダ名（例: `_agent`）ではなく `ai-context` を使うことで、利用ツールの入れ替え時にパス変更の影響を最小化する。
* **責務の分離:** `rules`・`workflows`・`skills` を物理的に分離することで、レビュー時に「行動規範」「手順」「専門知識」を混同しない。
* **入口の薄型化:** `.cursorrules` / `GEMINI.md` / `CLAUDE.md` / `.github/copilot-instructions.md` は参照案内だけに留め、実体は `ai-context` 側で一元管理する。
* **横断同期の容易化:** 1か所（`ai-context`）を編集すれば、Cursor・Claude Code・Antigravity・GitHub Copilot 向け設定の整合を維持しやすい。
* **段階導入のしやすさ:** 新しいエディタを追加する際は「入口ファイル追加 + `AGENTS.md` 参照」で導入でき、既存資産を再利用できる。
* **重複排除の原則:** `AGENTS.md` と `GEMINI.md` は root を正本（SSoT）とし、`ai-context/rules/` には重複コピーを置かない。

### 1-7. エディタ別の初期読込順（最短導入）
各エディタで新規セッションを開始した際は、次の順序で参照させると最も安定します。

1. **共通原則を固定する**
   `AGENTS.md` を最初に読み込ませ、言語・安全性・運用ルールを統一する。

2. **エディタ入口を読み込む**
   - Cursor: `.cursorrules`
   - Claude Code: `CLAUDE.md`
   - Antigravity: `GEMINI.md`
   - GitHub Copilot: `.github/copilot-instructions.md`

3. **共通資産を用途別に参照する**
   - ルール確認: `ai-context/rules/`
   - 実行手順: `ai-context/workflows/`
   - 専門知識: `ai-context/skills/`

4. **作業開始時の運用を固定する**
   実装前は `workflows` で計画を確定し、実装中は `rules` を遵守し、必要時のみ `skills` を呼び出す運用に統一する。

### 1-8. 正本ファイルと管理方針
更新の手間を最小化するため、以下を正本として運用します。

* `AGENTS.md`: 全エディタ共通の最上位ルール（正本）
* `GEMINI.md`: Antigravity向け入口ファイル（正本）
* `CLAUDE.md` / `.cursorrules` / `.github/copilot-instructions.md`: 参照案内のみを持つ薄い入口
* `ai-context/rules/`: 追加ルールの格納先（`AGENTS.md` / `GEMINI.md` の重複コピーは置かない）

---

## 2. Antigravityの基本設定（体系と保存先）

設定には「GUIからの直感的な操作（Customizations）」と「ファイルベースでの直接設定」の2種類があり、AIの振る舞いは主に以下の3つのレイヤーで制御されます。

### 2-1. 3つの主要な設定項目（AIの制御レイヤー）
* **Rules（ルール）**
  * **役割:** AIに常に守らせる「行動規範」や「掟」です（例：コーディング規約や日本語での出力など）。
  * **適用条件 (Activation Mode):** いつ適用するかを4種類から設定可能です。
    * `Always On`: 常に適用。
    * `Manual`: チャットで明示的に指定（`@ルール名`等）した時のみ適用。
    * `Model Decision`: AIが文脈から「必要だ」と自動判断した時に適用。
    * `Glob`: 指定した拡張子やパス（例:`*.ts`）のファイルを編集中のみ適用。
* **Workflows（ワークフロー）**
  * **役割:** 料理のレシピのように、特定のタスクをこなすための「手順書」です。
  * **使い方:** チャット欄で `/plan` や `/team` のようなスラッシュコマンドを入力することで、ユーザーが手動で起動します。
* **Skills（スキル）**
  * **役割:** AIの能力を拡張するための専門知識やスクリプト（ツール）です。
  * **使い方:** 人間が指示するのではなく、AIが「このタスクにはこの専門ツールが必要だ」と判断したときに自動で呼び出されます。

### 2-2. ファイルの保存場所（直接編集する場合）
GUIから作成した設定や、手動でファイルを作成する場合は、以下のパスに保存・反映されます。

* **グローバル設定（PC全体で共通）**
   * **Rules:** `~/.gemini/GEMINI.md` （Windowsの場合は `C:\Users\ユーザー名\.gemini\GEMINI.md`）
  * **Workflows:** `~/.gemini/antigravity/global_workflows/ファイル名.md`
  * **Skills:** `~/.gemini/antigravity/skills/`
  * **MCP設定:** `~/.gemini/antigravity/mcp_config.json`
* **ワークスペース設定（プロジェクト固有）**
   * **Rules:** `プロジェクトルート/ai-context/rules/ファイル名.md`
   * **Workflows:** `プロジェクトルート/ai-context/workflows/ファイル名.md`
   * **Skills:** `プロジェクトルート/ai-context/skills/`

*(※ 設定を追加・変更した際は、Antigravityを再起動しないと読み込まれないケースがある点に注意してください。)*

### 2-3. IDE本体の詳細設定（Settings）の推奨値
エージェントの自律性と安全性のバランスを取るため、ウィンドウ右下の **「Antigravity - Settings」** から以下の設定を行うことを推奨します：
* **Security:** `Strict Mode` を **Off** にする。
* **Artifact:** `Review Policy` を **Request Review** にし、必ず人間が確認を行うようにする。
* **Terminal:** コマンド実行の都度確認が出るのを防ぐため、`Command Auto Execution` を **Always Proceed** にする。
* **History & Context:** 過去の履歴によるコンテキスト汚染や不正確なRAG検索を防ぐため、`Conversation History` や `Knowledge` を **Off** にする。

---

## 3. ベストプラクティス：SSoT（Single Source of Truth）パターン

AntigravityとClaude CodeとCursorとGitHub Copilot（およびその他のCLI/IDE）の両方で汎用的に使える設定にするための鍵は、**ツール固有の設定ファイル（`GEMINI.md`や`CLAUDE.md`など）に直接ルールを書き込まず、CLI中立なディレクトリである `ai-context/` フォルダ（または共通の `AGENTS.md`）に設定を集約すること**です。

### 3-1. `GEMINI.md` / `CLAUDE.md` 用のテンプレート
これらのファイルは「薄いエントリポイント」とし、以下のどちらかの形式で共通ルールを読み込ませるだけにします。

**パターンA（テキストで指示）**
```markdown
プロジェクトのルールは `AGENTS.md` に記載されています。
必ず最初に `AGENTS.md` を読み、その指示に厳密に従ってください。
```

**パターンB（Front-matterを使用）**
```yaml
---
include: "AGENTS.md"
---
```

---

## 4. 実践的なプロンプト・設計パターン集

実務で汎用的に使える **Rules、Workflows、Skills** のプロンプト例です。コピー＆ペーストしてカスタマイズできます。

### 4-1. Rules（常時適用・行動規範）のプロンプト例
AIの「振る舞い」や「コーディング規約」など、常に守らせる原則を定義します。`ai-context/rules/` 配下、または共通の `AGENTS.md` に配置します。

```markdown
# 役割と基本方針 (Role & Philosophy)
あなたは日本の開発者を支援する責任あるシニアAIエンジニアです。
指示されたタスクをこなすだけでなく、システムの安全性と保守性を第一に考えて行動してください。

# 言語設定 (Language Settings)
- **対話・計画・出力:** 思考、説明、質問、回答、および計画書などの成果物はすべて自然な日本語で行ってください。
- **内部推論:** LLMの精度を最大限に引き出すため、内部的な推論が必要な場合は英語で行い、最終的な出力のみを日本語にするバイリンガル制御を許可します。

# コメンティング戦略 (Contextual Commenting)
コードは「未来の私」や「第三者」への手紙です。
- **Whyの記述:** コードの動作そのもの（What）ではなく、「なぜその処理が必要なのか（Why）」や「設計の意図」を必ず日本語コメントで残してください。

# 安全性と行動規範 (Safety & Conduct)
- **服従より安全優先:** `rm -rf` のような破壊的コマンドや大規模なファイルの削除・変更を行う前は、必ず影響範囲を確認し、実行前に計画を提示してください。
- **黙って失敗しない:** エラーや依存関係の不整合が発生した場合、黙ってファイルを書き換えるのではなく、問題の原因と選択肢を報告してください。
- **コミットメッセージの制約:** `update`、`fix`、`変更`、`対応` といった曖昧で具体性のない表現は絶対に使用しないでください。

# 計画書運用ルール (Plan Maintenance)
- **実装後の進捗同期は必須:** 開発タスクを実行した後は、実装計画書のタスク状況を必ず更新してください。日本語版と英語版がある場合は、両方を同じ進捗に同期してください。
```

### 4-2. Workflows（手動タスク手順）のプロンプト例
ユーザーがチャットでスラッシュコマンドを入力したときに手動で発動する手順書です。`ai-context/workflows/` 配下に配置します。
以下の例は、ハルシネーション（幻覚）や手戻りを防ぐための「いきなりコードを書かせず、まず計画を作らせる」ワークフローです。

```markdown
# ワークフロー: /plan (計画と実行の分離)

## 目的
実装を開始する前に、要件を整理し、AIと人間の間で認識のズレをなくすための実装計画（SPEC.md / TODO.md など）を作成します。

## 手順
このコマンドが呼び出されたら、以下のステップを厳密に実行してください。

1. **実装の禁止:** このフェーズでは絶対にコードの修正やファイルの作成、破壊的コマンドの実行を行わないでください。
2. **要件のヒアリング:** ユーザーが提示した要件（または `PLAN.md` などのメモ内容）を読み込みます。不明点や技術的な懸念があれば必ずユーザーに質問してください。
3. **計画書の作成:** 以下の構成で実装計画を出力してください。
   - 目的と概要
   - 変更が影響する範囲（影響を受けるファイル群）
   - 具体的なステップ（ToDoリスト）
4. **承認の待機:** 「この計画で実装を進めてよろしいでしょうか？」とユーザーに確認し、承認を得てから実際のコーディングフェーズに移行してください。
```

### 4-3. Skills（自動発動の専門知識）のプロンプト例
AIが文脈から判断してオンデマンドで読み込む拡張能力です。`ai-context/skills/` 内の `SKILL.md` に配置します。
**YAMLフロントマターで「発動条件（description）」を詳細に書く**ことが重要です。

```markdown
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
```

---

## 5. 運用・汎用化のためのベストプラクティス

1. **記憶の分業化（3ファイル体制）**
   ルールとコンテキストを以下の3ファイルに分割して管理することで、コンテキストの肥大化とセッション間の記憶喪失を防ぎます。
   - `AGENTS.md` : 不変のルール・原則
   - `MEMORY.md` : AIが自律的に書き込むプロジェクト固有の経験知・留意点
   - `HANDOFF.md` : セッション終了時に人間がキュレーションする引き継ぎ情報
2. **「やってはいけないこと」を明記する (Constraints)**
   AIに対しては「〜をしてください」よりも、Constraints（禁止事項）として「〜はしないでください」と設定する方が強力に機能し、安全性が高まります。
3. **1ファイルにつき100〜200行に収める**
   ルールファイルが長すぎるとAIの注意が分散し、精度が落ちます（100〜200行程度が最適）。長大になる場合は、外部ファイルとして分割し、インデックスのように参照させてください。
