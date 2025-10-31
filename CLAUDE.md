# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

pdfjs-distのPDF→画像変換品質を検証するための個人研究開発ツール。スタンドアロンのHTML/JavaScriptアプリケーションで、ビルドプロセスは不要。

## アーキテクチャ

- **構成**: 単一HTMLファイル (`index.html`) にCSS/JavaScriptを含む完結型アプリケーション
- **PDF.js統合**:
  - CDN版 (v2.16.105) を使用
  - npm依存関係として pdfjs-dist (v4.9.155) も含まれるが、実際の動作はCDN版に依存
- **日本語対応**: 外部CMap (https://package.protechidchecker.com/tests/pdfjs-dist/cmaps/) を使用

## 開発ワークフロー

```bash
# セットアップ
npm install

# 実行
# index.htmlをブラウザで直接開く（開発サーバー不要）
```

## コード構造の重要ポイント

### PDF変換のコアロジック

変換品質に影響する主要なパラメータは `renderPage()` 関数内で設定される:

1. **スケーリング**: `baseScale` と `maxWidth` による2段階スケール調整
2. **高DPI対応**: `devicePixelRatio` を使用したCanvas解像度の調整
3. **描画品質**: `imageSmoothingQuality` と `intent: 'print'` による高品質レンダリング
4. **Transform最適化**: Canvas transformまたは `setTransform()` による描画方法の選択

### レンダリングコンテキスト

```javascript
const renderContext = {
  canvasContext: context,
  viewport: viewport,
  intent: 'print' | 'display',  // 印刷品質 vs 表示品質
  renderInteractiveForms: boolean,
  transform: [outputScale, 0, 0, outputScale, 0, 0]  // オプション
};
```

## 技術的な注意点

- **Canvas解像度**: 物理ピクセル (canvas.width/height) とCSSピクセル (canvas.style.width/height) を区別して扱う
- **CMap**: 日本語PDF処理に必須。`cMapUrl` と `cMapPacked: true` の設定が必要
- **パスワード保護PDF**: `PasswordException` として処理し、エラー表示

## コード変更時の考慮事項

- UI変更時は左ペイン (300px固定) と右ペイン (flex) のレイアウトバランスを維持
- 新しい品質パラメータ追加時は `renderPage()` 関数と再変換ボタンのイベントハンドラを更新
- レスポンシブデザイン (768px breakpoint) を考慮
