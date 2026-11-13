# セキュリティ監査行動規範 (Security Audit Policy)

## 目的と基本思想
本規律は、Cloudflareが公開した脆弱性発見ハーネスの設計思想（`cloudflare/security-audit-skill` 由来）に基づき、ソースコード起点で確実なセキュリティ監査を自律的かつ安全に遂行するための行動規範を定めます。
机上のチェックリスト消化や誤検知の乱発を排し、「実効性のある境界破綻の確定」「敵対的・独立検証」「安全な隔離実行」を徹底します。

出典: [Cloudflare Security Audit Skill](https://github.com/cloudflare/security-audit-skill) (MIT License)

---

## 1. コア監査原則 (Core Audit Principles)

### 1.1 境界破綻の確定原則 (Established Boundary Failure Only)
- **推測・単なるチェックリスト違反の排除:** ソースコード上の完全な追跡（Source Trace）と、具体的な保護対象・影響境界（Trust Boundary）の破綻が実証・確定された事象のみを `confirmed`（確定脆弱性）と判定する。
- **具体性の義務:** 影響を受ける主体（Principal）、保護対象リソース（Resource）、具体的なセキュリティ上の帰結（Outcome）が特定できない候補は脆弱性として確定させてはならない。
- **未解決事実の明記:** 外部環境依存や動的検証の制約により確証が得られない場合は、推測で確定させず、未解決の具体的事実（Exact Unresolved Fact）を記録した `needs_validation`（要検証）として扱う。

### 1.2 敵対的・独立検証の原則 (Adversarial Validation)
- **発見者と検証者の完全分離:** 脆弱性候補（Candidate）を発見したエージェント（Hunter）自身による検証・確定を禁止する。
- **反証姿勢（Disprove）の徹底:** 候補を検証するエージェント（Verifier）は、候補が「脆弱性ではないこと（成立しないこと）」を証明する敵対的な視点から再調査を行い、確証バイアスを徹底排除する。

### 1.3 多層防御の欠落と脆弱性の峻別 (Defense-in-depth != Vulnerability)
- **防御層の確認:** 上流または外側の防御層（Layer A）によって攻撃や不正アクセスが確実に防止されている場合、下流や内側の防御層（Layer B）の欠落は脆弱性ではなく「堅牢化の注記（Hardening Note）」として分類する。
- **Severity requires impact:** 深刻度（Severity）はチェックリストからの逸脱ではなく、「悪用可能性（Likelihood）× 影響度（Impact）」の積によって厳格に評価する。

### 1.4 反復実行による網羅性確保 (Multiple Runs & Coverage Ledger)
- 1回の探索パスですべての脆弱性を検出し尽くしたとみなしてはならない。
- 過去の監査記録（`coverage-ledger.json` および `findings.json`）を追跡台帳として引き継ぎ、変更されたソースコードの再検証や、未検証領域（Gaps）の解消を段階的・累積的に実施する。

---

## 2. 判定ステータス規律 (Verdict Classifications)

| ステータス | 定義・要件 | 深刻度 (Severity) の付与 |
| :--- | :--- | :--- |
| **`confirmed`** | ソースコードの完全な追跡と境界破綻が客観的に確定した欠陥。 | **必須**（Critical / High / Medium / Low） |
| **`needs_validation`** | 境界破綻の疑いがあるが、未解決の外部依存や環境制約があり確定できない状態。未解決の事実を明記。 | **付与禁止**（Severityを付与してはならない） |
| **`rejected`** | 独立検証（Verifier）によって前提の誤りや防御機構の存在が確認され、反証・棄却された候補。理由を記録。 | なし |

---

## 3. 実行安全性と環境隔離基準 (Universal Execution Safety)

ターゲットコードのビルド、テスト実行、プロセス起動、フィクスチャ検証を行う場合は、必ず以下の隔離基準を遵守する。

1. **外部ネットワークの完全遮断:** 外部ネットワークへの通信は原則禁止。ローカルなクライアント/サーバー通信が必要な場合のみ独立したループバックに限定する。
2. **クリーンな環境変数:** ホストの環境変数は引き継がず、安全な最小限の許可リスト（Allowlist）から空環境に注入して実行する。認証情報や秘密鍵を絶対に渡さない。
3. **読み取り専用ソースと作業ディレクトリの限定:** ターゲットソースコードおよびツールチェーンは読み取り専用とし、書き込みは割り当てられた一時作業領域（`scratch/`）のみに限定する。
4. **リソース制限:** CPU、メモリ、プロセス数、ファイルサイズ、実行タイムアウトの上限を厳格に課す。
5. **本番・外部アクセスの厳禁:** 本番環境、外部API、公開サービス、実際のユーザーデータ、有料APIクォータに対するプローブやリクエスト送信は一切禁止する。テストには必ずダミーデータ（Fixtures）を用いる。
6. **基準不適合時の安全フォールバック:** 上記の安全隔離条件を満たせない環境では、ターゲットコードを実行してはならない。検証不能な理由を `needs_validation` のブロッカーとして記録し、安全な検証計画を提示する。

---

## 4. 2つの動作モード (Operating Modes)

### 4.1 ガイダンスモード (Guidance Mode)
- **適用場面:** 通常開発時のセキュリティに関する質疑、局所的なコードレビュー、脅威モデリング、単一脆弱性のトリアージ。
- **挙動:** 全6フェーズのワークフローを自動発動せず、成果物ファイル群の永続化も行わない。質問や要請に対してピンポイントで助言・回答する。

### 4.2 フル監査モード (Full Audit Mode)
- **適用場面:** `release_gate`（リリース前検査）、明示的なペネトレーションテストや網羅的セキュリティ監査の要請時。
- **挙動:** 全6フェーズ（Reconnaissance → Hunting → Candidate Validation → Structured Output → Independent Record Verification → Reporting）を実行し、構造化ログとレポートを生成する。

---

## 5. スキル導入と稼働規律 (Skill Installation & Invocation Discipline)

### 5.1 スキルのインストール方式 (Installation via Skills CLI)
- Cloudflare security-audit-skill は、公式の Skills CLI（`skills.sh`）を用いてインストールすることを原則とする。
  - プロジェクトスコープ導入:
    ```bash
    npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit
    ```
  - グローバル導入（推奨）:
    ```bash
    npx skills add https://github.com/cloudflare/security-audit-skill --skill security-audit --global
    ```
- **実行前提要件:**
  - ゼロ依存バリデータ（`validate-coverage-ledger.cjs`, `validate-findings.cjs`）を実行するための **Node.js** 環境が存在すること。
  - ツール使用および並列サブエージェント（Hunter / Verifier）をサポートするコーディングエージェント環境であること。

### 5.2 稼働トリガー構文 (Triggering the Skill)
- リリース前監査（Full Audit Mode）を発動させる際は、あいまいな表現を避け、公式Readme仕様に準拠した以下の定型構文で明示的にスキルを起動する。
  - 全体監査: `security audit this codebase, output to <output_dir>`
  - ディレクトリ指定: `find security vulnerabilities in ./src, output to <output_dir>`
  - スコープ/プロファイル指定: `do a security review, profile: <quick|standard|deep|scoped>, output to <output_dir>`

### 5.3 成果物の出力先規律 (Output Directory Policy)
- **リポジトリ汚染の防止:** 監査成果物（`coverage-ledger.json`, `findings.json`, `REPORT.md` 等）は、原則として対象リポジトリの外側に出力する。
  - デフォルト出力先: `~/security-audit-skill/<repo-name>/run-<N>`（`<N>` は連番）
- **リポジトリ内出力時の条件:** リポジトリ内に出力する場合は、必ずバージョン管理から除外（`.gitignore` に明記）されたディレクトリ（例: `.security-audits/run-<N>`）を指定しなければならない。

