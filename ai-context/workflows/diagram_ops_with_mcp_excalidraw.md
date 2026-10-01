---
description: mcp_excalidrawを使ってアーキテクチャ図・運用フロー図を継続作成するための標準運用手順。
---

# 目的

アーキテクチャ図や運用フロー図を、単発生成ではなく反復更新できる運用として定着させる。

# 前提

- Node.js 18以上
- Docker（任意）
- MCPクライアント（Claude Code / Codex CLI / Cursor / Antigravity いずれか）

# セキュリティ原則（必須）

1. サーバーは原則 `localhost` のみで運用する。
2. 認証なしAPI前提のため、外部ネットワークへ公開しない。
3. 図データに秘密情報（鍵、トークン、個人情報）を含めない。

# 推奨構成

- Canvas server: `http://localhost:3000`
- MCP server: stdio接続
- 保存先: `docs/diagrams/`
  - `*.excalidraw.json`
  - `*.elements.json`

# クイックスタート（ローカル）

```bash
npm ci
npm run build
# Terminal 1
PORT=3000 npm run canvas
# Terminal 2
EXPRESS_SERVER_URL=http://localhost:3000 node dist/index.js
```

# クイックスタート（Docker）

```bash
docker run -d -p 3000:3000 --name mcp-excalidraw-canvas ghcr.io/yctimlin/mcp_excalidraw-canvas:latest
```

MCP server側は、利用クライアントの設定に従って起動する。

# 日次運用フロー

## 1. 新規図を作る

1. 図の目的を定義する（例: API境界、障害時フロー）。
2. 要素を最小単位で作成する。
3. `describe_scene` で構造を確認する。
4. `align_elements` / `distribute_elements` で整形する。

## 2. 既存図を更新する

1. 既存JSONを `import_scene`。
2. 差分箇所のみ更新する。
3. 変更意図を図ファイル名またはコミット説明に残す。

## 3. エクスポートして保存する

1. `export_scene` でJSON保存。
2. 必要時に `export_to_image` でPNGを作成。
3. リポジトリへ保存し、レビュー可能にする。

# 命名規約

- 形式: `{domain}_{purpose}_{vYYYYMMDD}.excalidraw.json`
- 例:
  - `platform_architecture_v20260325.excalidraw.json`
  - `ops_incident_flow_v20260325.excalidraw.json`
- 詳細テンプレート: `docs/diagrams/NAMING_TEMPLATE.md`

# 失敗時の対処

- Canvasが更新されない:
  - `EXPRESS_SERVER_URL` とCanvas起動状態を確認する。
- 要素更新が失敗する:
  - `batch_create_elements` 実行後のID保持を確認する。
- データが消えた:
  - メモリ保持前提。定期的に `export_scene` で退避する。

# Done条件

- 1件以上のアーキテクチャ図をJSONで保存済み。
- 1件以上の運用フロー図をJSONで保存済み。
- 保存先と命名規約がチーム合意されている。
