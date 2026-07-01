# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

iPhoneカメラで撮影した食事券の番号をOCR読み取りし、PC側で一覧表示・コピーできるデスクトップアプリ。

## スタック

- **フレームワーク**: Electron + TypeScript（electron-vite）
- **パッケージマネージャ**: pnpm
- **OCR**: Tesseract.js
- **iPhone接続**: Electron内Expressサーバー、同一WiFiのiPhoneブラウザから `getUserMedia` でカメラ接続
- **テスト**: Vitest
- **Linter/Formatter**: ESLint + Prettier

## 開発コマンド

```bash
pnpm install        # 依存インストール
pnpm dev            # 開発サーバー起動
pnpm build          # ビルド
pnpm test           # テスト実行
pnpm test:watch     # テストウォッチモード
pnpm lint           # ESLint
pnpm typecheck      # 型チェック
```

## 開発フロー

**TDDで進める。** 実装前にテストを書き、テストが通る最小限の実装をする。

1. テスト作成（Red）
2. 最小実装（Green）
3. リファクタ（Refactor）

## 券番号の変換ルール

OCR読み取り後、以下のルールで変換して出力する。

| 入力フォーマット | 出力 |
|---|---|
| `[A-Z]-[a-z]-XXXXXX` | `99` + (a=0, b=1, c=2…) + `XXXXXX` |
| `[A-Z]-XXXXX` | `XXXXX`（そのまま） |

例: `A-a-048712` → `99048712`、`A-46124` → `46124`

## アーキテクチャ

```
Main Process（Node.js）
├── Expressサーバー（iPhone接続用HTTP）
├── Tesseract.js（OCR処理）
├── データ永続化（JSONファイル）
└── IPC通信

Renderer Process（Web UI）
├── 番号リスト表示
├── 手動編集・削除
└── まとめコピーボタン

iPhone（ブラウザ）
└── getUserMedia → フレーム送信
```
