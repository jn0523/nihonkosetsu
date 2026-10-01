# Codex & build-ios-apps iOS Development Playbook (iOSアプリ開発実践プロンプト集)

本ドキュメントは、OpenAI Codex および `plugins/build-ios-apps` プラグインを活用して、高品質なSwiftUIアプリをCLI優先・自律ループで構築・検証・デバッグするための実践プロンプト集（Cheatsheet）です。

---

## 1. 開発環境・プラグイン・スキル構成

### 1-1. コアツール
- **Codex**: コード生成、リファクタリング、プロジェクトスキャフォールディングを自律実行するエージェント。
- **plugins/build-ios-apps**: iOSアプリ開発に特化したOpenAI公式プラグイン。SwiftUIビュー構築、リファクタリング、デバッグワークフローを提供。
- **XcodeBuildMCP**: スキーム一覧、ターゲットビルド、シミュレータ制御（起動・タップ・入力・スクロール）、スクリーンショット撮影、ログストリーミング、LLDBアタッチをCLIから操作可能にするMCPサーバー。
- **xcodebuild / Tuist**: Xcode GUIを開かずにターミナルからビルド・テストを実行するCLI基盤。

### 1-2. 専門スキル一覧
| スキル名 | 役割・活用シーン |
| :--- | :--- |
| **SwiftUI expert** | SwiftUIのベストプラクティスが組み込まれた汎用性の高い設計・実装スキル。 |
| **SwiftUI Pro** | 最新API、保守性、アクセシビリティ、パフォーマンスの包括的レビュー。 |
| **SwiftUI view refactor** | 肥大化したViewを専用サブビューへ分割し、MVファースト・安定したデータフローへ再構築。 |
| **SwiftUI patterns** | `@Observable` および `@Environment` を用いた予測可能なアーキテクチャパターンの適用。 |
| **SwiftUI performance** | レンダリング遅延や不要な再描画パスを検知し、優先度付きで最適化。 |
| **Swift concurrency expert** | Swift 6 / Concurrency の複雑なコンパイル警告・データ競合エラーを解消。 |
| **Liquid Glass expert** | iOS 26の新しいLiquid Glass APIへの移行と、旧OS向けフォールバックの設計。 |

---

## 2. 実践プロンプト集（スターターテンプレート）

### 2-1. 新規機能追加・オンボーディングスライス実装
既存プロジェクトに新機能や画面フローを追加する際の標準プロンプト。

```text
Add the [FeatureName, e.g. onboarding flow] for this SwiftUI app.

Constraints:
- Reuse existing models, navigation patterns, and shared utilities.
- Use XcodeBuildMCP to list the right targets or schemes, build the app, launch it, and capture screenshots if you need visual verification.
- Keep the implementation focused on iPhone and iPad unless I explicitly ask for a shared iOS/macOS abstraction.
- Tell me exactly which scheme, simulator, and checks you used.

Implement the slice, verify it with the smallest relevant build or run loop, and summarize what changed.
```

---

### 2-2. SwiftUI画面のリファクタリング（MVファースト徹底）
巨大化したSwiftUIビューの振る舞い・外観を1ミリも崩さず、保守性の高い構造へ分割するプロンプト。

```text
Use the Build iOS Apps plugin and its SwiftUI view refactor skill to clean up [NameOfScreen.swift] without changing what the screen does or how it looks.

Constraints:
- Preserve behavior, layout, navigation, and business logic unless you find a bug that must be called out separately.
- Default to MV, not MVVM. Prefer `@State`, `@Environment`, `@Query`, `.task`, `.task(id:)`, and `onChange` before introducing a new view model, and only keep a view model if this feature clearly needs one.
- Reorder the view so stored properties, computed state, `init`, `body`, view helpers, and helper methods are easy to scan top to bottom.
- Extract meaningful sections into dedicated `View` types with small explicit inputs, `@Binding`s, and callbacks. Do not replace one giant `body` with a pile of large computed `some View` properties.
- Move non-trivial button actions and side effects out of `body` into small methods, and move real business logic into services or models.
- Keep the root view tree stable. Avoid top-level `if/else` branches that swap entirely different screens when localized conditional sections or modifiers are enough.
- Fix Observation ownership while refactoring: use `@State` for root `@Observable` models on iOS 17+, and avoid optional or delayed-initialized view models unless the UI genuinely needs that state shape.
- After each extraction, run the smallest useful build or test check that proves the screen still behaves the same.

Deliver:
- the refactored screen and any extracted subviews
- a short explanation of the new subview boundaries and data flow
- any places where you intentionally kept a view model and why
- the validation checks you ran to prove behavior stayed intact
```

---

### 2-3. Liquid Glass 移行（iOS 26最新マテリアルデザイン）
カスタムブラーや独自マテリアルをiOS 26のネイティブLiquid Glassへ移行し、旧OSへのフォールバックを確保するプロンプト。

```text
Use the Build iOS Apps plugin and its SwiftUI Liquid Glass skill to migrate one high-traffic flow in this app to Liquid Glass.

Constraints:
- Treat this as an iOS 26 + Xcode 26 migration, but preserve a non-glass fallback for earlier deployment targets with `#available(iOS 26, *)`.
- Audit the flow first. Call out custom backgrounds, blur stacks, chips, buttons, sheets, and toolbars that should become native Liquid Glass and call out surfaces that should stay plain content.
- Prefer system controls and native APIs like `glassEffect`, `GlassEffectContainer`, `glassEffectID`, `.buttonStyle(.glass)`, and `.buttonStyle(.glassProminent)` over custom blurs. Use `glassEffectID` with `@Namespace` only when a real morphing transition improves the flow.
- Apply `glassEffect` after layout and visual modifiers, keep shapes consistent, and use `.interactive()` only on controls that actually respond to touch.
- Use XcodeBuildMCP to build and run on an iOS 26 simulator, capture screenshots for the migrated flow, and mention exactly which scheme, simulator, and checks you used.

Deliver:
- a concise migration plan for the flow
- the implemented Liquid Glass slice
- the fallback behavior for pre-iOS 26 devices
- the simulator validation steps and screenshots you used
```

---

### 2-4. App Intents / App Shortcuts の追加
アプリのアクションとエンティティをSiri、Spotlight、ショートカット、将来のアシスタント駆動UIへ公開するプロンプト。

```text
Use the Build iOS Apps plugin to audit this iOS app and add App Intents for the actions and entities that should be exposed to the system.

Constraints:
- Start by identifying the app's highest-value user actions and core objects that should be available outside the app in Shortcuts, Siri, Spotlight, widgets, controls, or newer assistant-driven system surfaces.
- Keep the first pass focused. Pick a small set of intents that are genuinely useful without opening the full app, plus any open-app intents that should deep-link into a specific screen or workflow.
- Define app entities only for the data the system actually needs to understand and route those actions. Do not mirror the entire internal model layer if a smaller entity surface is enough.
- Add App Shortcuts where they make the experience more discoverable, and choose titles, phrases, and display representations that would make sense in Siri, Spotlight, and Shortcuts.
- If the app needs to handle the intent inside the main UI, route the result back into the app cleanly and explain how the app scene reacts to that handoff.
- Build and validate the app after the first pass, then summarize which actions, entities, and system surfaces are now supported.

Deliver:
- the recommended intent and entity surface for a first release
- the implemented intents, entities, and App Shortcuts
- how the app routes or handles those intents at runtime
- which Apple system experiences this unlocks now and which ones are logical next steps
```

---

### 2-5. シミュレータ上での自律バグ再現・デバッグ
XcodeBuildMCPを用いてシミュレータ上でバグを直接再現・特定し、最小限の修正と証拠を収集するプロンプト。

```text
Use the Build iOS Apps plugin and XcodeBuildMCP to reproduce this bug directly in Simulator, diagnose the root cause, and implement a small fix.

Bug report:
[Describe the expected behavior, the actual bug, and any known screen or account setup.]

Constraints:
- First check whether a project, scheme, and simulator are already selected. If not, discover the right Xcode project or workspace, pick the app scheme, choose a simulator, and reuse that setup for the rest of the session.
- Build and launch the app in Simulator, then confirm the right screen is visible with a UI snapshot or screenshot before you start interacting with it.
- Drive the exact reproduction path yourself by tapping, typing, scrolling, and swiping in the simulator. Prefer accessibility labels or IDs over raw coordinates, and re-read the UI hierarchy before the next action when the layout changes.
- Capture evidence while you debug: screenshots for visual state, simulator logs around the failure, and LLDB stack frames or variables if the bug looks like a crash or hang.
- If the simulator is not already booted, boot one and tell me which device and OS you chose. If credentials or a special fixture are required, pause and ask only for that missing input.
- Make the smallest code change that addresses the bug, then rerun the simulator flow and tell me exactly how you verified the fix.

Deliver:
- the reproduction steps Codex executed
- the key screenshots, logs, or stack details that explained the bug
- the code fix and why it works
- the simulator and scheme used for final verification
```

---

## 3. 実践の黄金律（Best Practices）

1. **まずは分割、それからアーキテクチャ:**
   - 画面が巨大化したときは、新しい抽象化レイヤー（ViewModel等）をいきなり導入せず、まずセクションViewを切り出す。それだけでViewModelが不要になるケースが大半である。
2. **サブビューには最小限の型を渡す:**
   - 親の全状態を丸ごと渡すのではなく、`let` 値、`@Binding`、目的が限定されたコールバックのみを渡すことで、プレビューが容易になり結合度が下がる。
3. **「変更しなかったこと」の明示:**
   - 安全なリファクタリングの報告では、「変更した部分」だけでなく、「意図的に変更しなかったビジネスロジックやナビゲーション」を報告させることで、レビュー工数を大幅に圧縮できる。
4. **言葉ではなく証拠（Evidence）で確認:**
   - 「直しました」を信用せず、シミュレータでの実行ログ、スクリーンショット、スキーム名を必ず確認する。
