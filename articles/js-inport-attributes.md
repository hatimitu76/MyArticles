---
title: "JavaScriptのImport AttributesでJSONとCSSをimportする"
emoji: "📝"
type: "tech"
topics: ["javascript", "typescript", "ecmascript", "json", "css"]
published: true
---

## はじめに

こんにちは、ハチミツです。

私はAstroとPages CMSを組み合わせて自身のブログを構築しています。Pages CMSではMarkdownやJSON、YAMLを扱えますが、ブログの設定をTypeScriptで書いている関係で設定をPages CMSで扱えませんでした。その解決策としてJSONを活用する中で発見したJavaScriptの新機能を共有します。

## 対象読者

- JavaScriptでJSONやCSSを扱う開発者
- モダンなJavaScript機能に興味がある方

## Import Attributesの基本的な使い方

### JSONの場合

従来のコード:
```js
const res = await fetch("https://json.test/test.json");
const JSONobj = await res.json();
```

Import Attributesを使用:
```js
import JSONobj from "https://json.test/test.json" with { type: "json" };
// または
const JSONobj = await import("https://json.test/test.json", { with: { type: "json" } });
```

### CSSの場合

従来のコード:
```js
const res = await fetch("https://css.test/test.css");
const sheet = new CSSStyleSheet();
await sheet.replace(await res.text());
document.adoptedStyleSheets = [ sheet ];
```

Import Attributesを使用:
```js
import sheet from "https://css.test/test.css" with { type: "css" };
// または
const sheet = await import("https://css.test/test.css", { with: { type: "css" } });
document.adoptedStyleSheets = [ sheet ];
```

## Import Attributesの詳細

Import Attributesを使用すると、JavaScriptモジュールと同様の方法でJSONやCSSをインポートできます。`with`キーワードでタイプを指定します。

このAPI導入により、JSON・CSSの読み込みがより宣言的で簡潔になりました。

Node.jsでもJSONのimportに対応しており、ローカルファイルにも使用できます。設定ファイルの読み込みなど、実用的なユースケースが期待できます。

ダイナミックインポートにも対応しているため、APIリクエストなどにも活用できます：

```js
async function fetchAPI(num) {
  return import(`https://api.test/v1/users/${num}.json`, { with: { type: "json" } });
}
```

ただし、この機能はFirefoxでまだ実装されていません（2025/2/22現在）。ブラウザでの本番利用は、もう少し様子を見ることをお勧めします。

## 参考リンク

### MDN

https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/import/with

### caniuse

https://caniuse.com/?search=JavaScript%20statement%3A%20import%3A%20Import%20attributes%20(%60with%60%20syntax)
