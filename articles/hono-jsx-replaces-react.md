---
title: Hono JSXでReactを置き換える
emoji: "🔥"
type: tech
topics: ["react", "hono", "honoJSX"]
published: true

createdAt: 2024-12-19
updateAt: 2025-05-16
---

:::message alert

2025-05-16追記
Chadcn UIが動かないことを追記しました。

:::

皆さん、Honoは使っていますか？　彼はどこでも素早く動くTypeScript製のバックエンド(最近はフルスタックし始めてるけど)フレームワークです。

最近Hono付属のJSXでReactを置き換えられるということに気付いたので共有していきます。

### やり方

何かでReactを使えるようにした後、このコマンドを実行します。

```sh
npm install react@npm:@hono/react-compat react-dom@npm:@hono/react-compat
```

これを実行するとHono JSXがいい感じにReactを置き換えてくれます。バリ簡単ですね。

### 利点は？

単純に言うと劇的に軽くなります。

Reactは47.8KBのスクリプトをブラウザに送りますが、Hono JSXは2.8KBのスクリプトで済みます。Reactの約5％ですね。(どうなってんだこれ)

### 互換性は大丈夫なの？

`hono/jsx/dom`は次のフックを実装しているらしいです。

+ `useState()`
+ `useEffect()`
+ `useRef()`
+ `useCallback()`
+ `use()`
+ `startTransition()`
+ `useTransition()`
+ `useDeferredValue()`
+ `useMemo()`
+ `useLayoutEffect()`
+ `useReducer()`
+ `useDebugValue()`
+ `createElement()`
+ `memo()`
+ `isValidElement()`
+ `useId()`
+ `createRef()`
+ `forwardRef()`
+ `useImperativeHandle()`
+ `useSyncExternalStore()`
+ `useInsertionEffect()`
+ `useFormStatus()`
+ `useActionState()`
+ `useOptimistic()`

見たことある面々が揃ってますね。

どうやらほぼほぼ互換性はできているそうで、ドキュメントには「使い方はReactをみてね」と書かれていました。すごすぎませんか？

### 動いた場所

自分が確認した限り、ViteのReactとRemixのテンプレートが動きました。

### 動かなかった場所

Chadcn UIが動きませんでした。型定義でエラーが出ていました。
