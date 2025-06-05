# GPK FWMaker

**QMK/Vialファームウェア生成API** - Docker化されたキーボードファームウェアビルドサービス

[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)](https://nodejs.org/)
[![QMK](https://img.shields.io/badge/QMK-1e88e5?style=flat&logo=qmk&logoColor=white)](https://qmk.fm/)
[![Vial](https://img.shields.io/badge/Vial-purple?style=flat)](https://get.vial.today/)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/darakuneko/gpk_fwmaker)

[🌐 English](README.md) | 🇯🇵 日本語

## 概要

GPK FWMakerは、QMK/Vialキーボードファームウェアの生成をAPIで提供するコンテナ化されたサービスです。キーボードファイルからファームウェアまで、全てのプロセスを自動化します。

### 主な機能

- **QMK/Vialファームウェアビルド** - APIによる自動ビルド
- **ファイル変換** - KLE JSON → QMK/Vial、Vial → C キーマップ
- **キーボードファイル生成** - 自動的なプロジェクトファイル作成
- **Vial用ユニークID生成** - Vialプロジェクト用ID
- **完全Docker化** - 設定済み環境で即座に利用可能

### 最新の状態

- **TypeScript完全移行済み** - 型安全性とコード品質向上  
- **Vitestテスト環境構築済み** - 包括的なAPIテスト  
- **Docker環境動作確認済み** - 本番運用準備完了  
- **品質管理システム導入** - 自動チェック・テスト実行

---

## クイックスタート

### 前提条件

- **Docker Desktop** インストール済み  
  ダウンロード: https://www.docker.com

### 1. プロジェクトダウンロード

```bash
# GitHubからダウンロード
git clone https://github.com/darakuneko/gpk_fwmaker.git
cd gpk_fwmaker

# または ZIP ダウンロード
# https://github.com/darakuneko/gpk_fwmaker/archive/refs/heads/main.zip
```

### 2. Docker環境構築・起動

```bash
# Dockerイメージビルド
docker compose build

# サービス起動
docker compose up -d
```

### 3. 動作確認

```bash
# サービス起動確認
curl http://127.0.0.1:3123/
# → "GPK FWMaker!" が返る

# QMKタグ一覧取得
curl http://127.0.0.1:3123/tags/qmk
# → ["0.29.4", "0.29.3", ...] が返る
```

### 4. ファームウェアビルド例

```bash
# QMKファームウェアビルド
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' \
  http://127.0.0.1:3123/build/qmk

# Vialファームウェアビルド  
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "vial"}' \
  http://127.0.0.1:3123/build/vial
```

---

## ファイル管理

### GPKFW ディレクトリ

起動後、ホームディレクトリに `GPKFW` フォルダが自動作成されます：

```
GPKFW/
├── keyboards/     # キーボードファイル配置場所
├── firmware/      # 生成されたファームウェア
└── generated/     # 自動生成ファイル
```

**例**:
- Windows: `C:\\Users\\[username]\\GPKFW`
- macOS: `/Users/[username]/GPKFW`
- Linux: `/home/[username]/GPKFW`

---

## API リファレンス

**ベースURL**: `http://127.0.0.1:3123`

### ファームウェアビルド

#### QMKファームウェアビルド
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "default", "tag": "0.19.3"}' \
  http://127.0.0.1:3123/build/qmk
```
- **kb** (必須): キーボード名
- **km** (必須): キーマップ名  
- **tag** (必須): QMKバージョンタグ

#### Vialファームウェアビルド
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "reviung/reviung41", "km": "vial"}' \
  http://127.0.0.1:3123/build/vial
```
- **kb** (必須): キーボード名
- **km** (必須): キーマップ名
- **commit** (任意): Vialコミット指定

#### カスタムファームウェアビルド
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"fw": "custom", "kb": "my_keyboard", "km": "default"}' \
  http://127.0.0.1:3123/build/custom
```

### 情報取得

#### QMKタグ一覧取得
```bash
curl http://127.0.0.1:3123/tags/qmk
# → ["0.29.4", "0.29.3", "0.29.2", ...]
```

#### キーボード一覧取得
```bash
# QMKキーボード一覧
curl http://127.0.0.1:3123/list/qmk

# Vialキーボード一覧  
curl http://127.0.0.1:3123/list/vial
```

#### Vial用ユニークID生成
```bash
curl http://127.0.0.1:3123/generate/vial/id
# → "0x12345678"
```

### ファイル変換

#### KLE JSON → QMK/Vial変換
```javascript
const formData = new FormData()
formData.append('kle', kleJsonFile)
formData.append('params', JSON.stringify({
  kb: 'my_keyboard',
  mcu: 'atmega32u4',
  user: 'username',
  vid: '0xFEED',
  pid: '0x0001',
  option: 1,  // 0: QMK, 1: Vial, 2: via.json only
  rows: 'GP0,GP1,GP2,GP3',
  cols: 'GP4,GP5,GP6,GP7'
}))

fetch('http://127.0.0.1:3123/convert/kle/qmk', {
  method: 'POST',
  body: formData
})
```

#### info.json + KLE → via.json変換
```javascript
const formData = new FormData()
formData.append('info', infoJsonFile)
formData.append('kle', kleJsonFile)

fetch('http://127.0.0.1:3123/convert/via/json', {
  method: 'POST',
  body: formData
})
```

#### Vial JSON → C keymap変換
```javascript
const formData = new FormData()
formData.append('vial', vialJsonFile)

fetch('http://127.0.0.1:3123/convert/vial/json', {
  method: 'POST',
  body: formData
})
```

### 管理機能

#### リポジトリ更新
```bash
# QMKリポジトリ更新
curl http://127.0.0.1:3123/update/repository/qmk

# Vialリポジトリ更新
curl http://127.0.0.1:3123/update/repository/vial
```

#### QMKキーボードファイル生成
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"kb": "my_keyboard", "mcu": "atmega32u4", "layout": "60_ansi", "user": "username"}' \
  http://127.0.0.1:3123/generate/qmk/file
```

---

## 開発環境

### ローカル開発

```bash
cd server

# 依存関係インストール
npm install

# TypeScript型チェック
npm run type-check

# テスト実行
npm run test

# リンティング
npm run lint:ts

# ビルド
npm run build

# 開発サーバー起動
npm run dev
```

### 品質チェック

```bash
# 総合品質チェック（型チェック + リンティング）
npm run check

# テスト実行
npm run test

# カバレッジ付きテスト
npm run test:coverage
```

### テスト環境

```bash
# 単体テスト
npm run test:unit

# APIテスト
npm run test:api

# ウォッチモード
npm run test:watch

# UIダッシュボード
npm run test:ui
```

---

## アーキテクチャ

### プロジェクト構成

```
gpk_fwmaker/
├── Dockerfile              # Docker設定
├── compose.yml              # Docker Compose設定
├── server/                  # Node.js APIサーバー
│   ├── src/
│   │   ├── app.ts             # Express メインアプリ
│   │   ├── command.ts         # QMK/Vialコマンド実行
│   │   └── vial2c/
│   │       └── vial2c.ts      # Vial→Cキーマップ変換
│   ├── tests/                 # テストスイート
│   ├── dist/                  # ビルド出力
│   └── tsconfig.json          # TypeScript設定
└── firmware-scripts/        # Python変換スクリプト
```

### 技術スタック

- **バックエンド**: Node.js + Express + TypeScript
- **スクリプト**: Python + Flask
- **テスト**: Vitest + SuperTest
- **コンテナ**: Docker + Docker Compose
- **ビルドツール**: TypeScript Compiler
- **品質管理**: ESLint + TypeScript

---

## トラブルシューティング

### よくある問題

#### ビルドエラー
```bash
error: branch 'x.x.x' not found.
```
**解決方法**: リポジトリを更新
```bash
curl http://127.0.0.1:3123/update/repository/qmk
```

#### Docker起動失敗
**解決方法**: Docker Desktopが起動していることを確認

#### ポート競合
**解決方法**: ポート3123が使用されていないことを確認
```bash
lsof -i :3123
```

### サポート

- [Issues](https://github.com/darakuneko/gpk_fwmaker/issues)

---

## 関連プロジェクト

### 詳細ガイド
**DeepWiki - GPK FWMaker**  
https://deepwiki.com/darakuneko/gpk_fwmaker

### GUI バージョン
**GPK FWBuilder**  
https://github.com/darakuneko/gpk_fwbuilder

### 参考プロジェクト
**Firmware Scripts** by zykrah  
https://github.com/zykrah/firmware-scripts

### 公式ドキュメント
- [QMK Firmware](https://docs.qmk.fm)
- [Vial](https://get.vial.today/docs/porting-to-via.html)

---

## 開発者サポート

**コーヒーを奢る**  
[Amazon Wishlist](https://www.amazon.co.jp/hz/wishlist/ls/66VQJTRHISQT) | [Ko-fi](https://ko-fi.com/darakuneko)

---

## ライセンス

このプロジェクトは [MIT License](LICENSE) の下で公開されています。

---

<div align="center">

**GPK FWMaker - QMK/Vialファームウェア生成をもっと簡単に**

Made with ❤️ by [darakuneko](https://github.com/darakuneko)

</div>