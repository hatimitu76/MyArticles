---
title: Astro製のサイトにSNSの投稿を埋め込む
emoji: "📱"
type: tech
topics: ["astro", "astro5", "SSG", "SNS", "埋め込み"]
published: true

createAt: 2024-12-21
updateAt: 2024-12-21
---

:::message

こちらは2024年12月21日に私の[個人ブログ](https://hatmt.com/blog)で公開されたものを編集したものです。

:::

つい最近、Astro 5.0(記事公開時点)がリリースされました。このリリースでContent Layerがリリースされ、様々なデータソースからデータを読み込めるようになりました。

このContent Layerを使ってMisskeyとBlueskyの投稿を取得していきます。

Astro 4以前のContent Collectionでは読み込めないので、バージョンアップとコードの変更をしておいてください。

## 今回使うライブラリ

+ @ascorbic/bluesky-loader
+ @ascorbic/feed-loader

### リポジトリ

https://github.com/ascorbic/astro-loaders

## Bluesky

### インストール

```sh
npm install @ascorbic/bluesky-loader
```

### 設定

インストールが終わったら`src/content.config.ts`を編集しに行きましょう。

```ts
import { defineCollection } from "astro:content";
import { authorFeedLoader } from "@ascorbic/bluesky-loader";

const bskyPosts = defineCollection({
  loader: authorFeedLoader({
    identifier: "your handle",
    late: 50
  }),
});

export const collections = { bskyPosts };
```

`your handle`の部分には@を抜いたあなたのハンドルを入力してください。
`late`は好きなように設定してください。

これを書いておけばあなたのBlueskyの投稿を、AstroでMarkdownを扱うときのノリで扱えるようになります。ちなみにリポストも含まれるので自分の投稿だけを表示したいほうはフィルタリングをする必要があります。

Markdownの場合はフロントマターの情報が入っている`entry.data`には、投稿者の情報や投稿本体などが入っています。

参考のために`entry`を`JSON.stringify()`したものを置いておきます。

:::details 開く。

```json
{
    "id": "at://did:plc:ruwnuvzigdl3527oe3vjrqwj/app.bsky.feed.post/3ldgsirsxns2y",
    "data": {
      "uri": "at://did:plc:ruwnuvzigdl3527oe3vjrqwj/app.bsky.feed.post/3ldgsirsxns2y",
      "cid": "bafyreidkhldm7tzvkijvhhcnyxmyiuxrjoocvj7m3yndrbqawldknfeqqa",
      "author": {
        "did": "did:plc:ruwnuvzigdl3527oe3vjrqwj",
        "handle": "frieren.websunday.net",
        "displayName": "葬送のフリーレン 公式",
        "avatar": "https://cdn.bsky.app/img/avatar/plain/did:plc:ruwnuvzigdl3527oe3vjrqwj/bafkreidqdl76vgxt7rig254rkfi55tmjnbhwyexnvdenas35h3tsxskbbm@jpeg",
        "associated": {
          "chat": {
            "allowIncoming": "following"
          }
        },
        "labels": [],
        "createdAt": "2024-02-13T04:24:52.053Z"
      },
      "record": {
        "$type": "app.bsky.feed.post",
        "createdAt": "2024-12-16T17:00:18.363Z",
        "langs": [
          "ja"
        ],
        "text": "mixi2 \\n\\nmixi.social/invitations/...",
        "embed": {
          "$type": "app.bsky.embed.external",
          "external": {
            "uri": "https://mixi.social/invitations/@frieren_pr/8ZCCaWoXfAoJTF2aaX8iW9",
            "title": "[mixi2への招待] 『葬送のフリーレン』 公式さんとはじめよう",
            "description": "週刊少年サンデー連載中『葬送のフリーレン』（原作：山田鐘人／作画：アベツカサ）の原作公式アカウントです。更新情報などを編集部員がつぶやきます。第13巻&特装版・小説・付箋ブック発売中！TVアニメ第2期制作決定。【ファンアートタグ】→#フリーレンFA",
            "thumb": {
              "$type": "blob",
              "ref": {
                "$link": "bafkreiemlnexql5vdnncb6orxo2wrpkurdm57tdbpr7ut5bs5lq7a53qhu"
              },
              "mimeType": "image/jpeg",
              "size": 191082
            }
          }
        },
        "facets": [
          {
            "index": {
              "byteStart": 8,
              "byteEnd": 35
            },
            "features": [
              {
                "$type": "app.bsky.richtext.facet#link",
                "uri": "https://mixi.social/invitations/@frieren_pr/8ZCCaWoXfAoJTF2aaX8iW9"
              }
            ]
          }
        ]
      },
      "embed": {
        "$type": "app.bsky.embed.external#view",
        "external": {
          "uri": "https://mixi.social/invitations/@frieren_pr/8ZCCaWoXfAoJTF2aaX8iW9",
          "title": "[mixi2への招待] 『葬送のフリーレン』 公式さんとはじめよう",
          "description": "週刊 少年サンデー連載中『葬送のフリーレン』（原作：山田鐘人／作画：アベツカサ）の原作公式アカウ ントです。更新情報などを編集部員がつぶやきます。第13巻&特装版・小説・付箋ブック発売中！TVアニメ第2期制作決定。【ファンアートタグ】→#フリーレンFA",
          "thumb": "https://cdn.bsky.app/img/feed_thumbnail/plain/did:plc:ruwnuvzigdl3527oe3vjrqwj/bafkreiemlnexql5vdnncb6orxo2wrpkurdm57tdbpr7ut5bs5lq7a53qhu@jpeg"
        }
      },
      "replyCount": 9,
      "repostCount": 287,
      "likeCount": 1104,
      "quoteCount": 16,
      "indexedAt": "2024-12-16T17:00:23.447Z",
      "labels": []
    },
    "rendered": {
      "html": "mixi2 <br/ >\n<br/ >\n<a href=\"https://mixi.social/invitations/@frieren_pr/8ZCCaWoXfAoJTF2aaX8iW9\">mixi.social/invitations/...</a>"
    },
    "collection": "bskyPosts"
}
```
:::

これにAPIを結構いろんなことができそうですね。

### 投稿を埋め込む

投稿を埋め込むときは、**絶対にAstro Embedを使わない**でください！

Astro EmbedはNo JSなAstroの埋め込みライブラリです。No JSであるのは非常に助かりますが、投稿一覧を作成するときにはその性質が悪い方向に作用してしまいます。

**APIのレート制限にすぐに達してしまうのです！**

どうやらAstro Embedは内部でBlueskyのAPIを叩いて静的なHTMLを出力しているようです。ブログに一つ二つ投稿を埋め込むくらいならレートに達することはありませんが一つのアカウントのすべてのポストを取得し、それを何度もリロードするとどうでしょうか？　すぐにレートに達しますね()

そこで、公式の埋め込みを使用します。

生成されるHTMLはこんな感じです。

```html
<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:kenywqun3liygj5q7xverc62/app.bsky.feed.post/3lddnc7fsws26" data-bluesky-cid="bafyreidxdwl3dsydu3uitcc4qcza66fdnqv53hb26wg34kmdtq3pnxd7my"><p lang="ja">家のUbuntuをオーバーテクノロジーで超安全にした
方法
激長パスフレーズ付きed25519鍵 + Cloudflare Tunnel + Cloudflare Access
不正アクセスするには、SSHのドメインと俺のスマホとGoogleのパスと鍵とパスフレーズが必要
安心感がやばい</p>&mdash; ハチミツ (<a href="https://bsky.app/profile/did:plc:kenywqun3liygj5q7xverc62?ref_src=embed">@hatmt.com</a>) <a href="https://bsky.app/profile/did:plc:kenywqun3liygj5q7xverc62/post/3lddnc7fsws26?ref_src=embed">2024年12月15日 19:49</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>
```

ここから機能的ではない要素を弾くとこうなります。

```html
<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:kenywqun3liygj5q7xverc62/app.bsky.feed.post/3lddnc7fsws26" data-bluesky-cid="bafyreidxdwl3dsydu3uitcc4qcza66fdnqv53hb26wg34kmdtq3pnxd7my"></blockquote>
<script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>
```

これさえあれば埋め込みが成立します。

`uri`は`entry.data.url`で、`cid`は`entry.data.cid`で取得できるのでこんな感じになります。

```ts
import { getCollection } from "astro:content";
const bskyPosts = await getCollection("bskyPosts")
```

```tsx
{
  bskyPosts.map((post) => (<blockquote
    class="bluesky-embed"
    data-bluesky-uri={post.data.uri}
    data-bluesky-cid={post.data.cid}>
  </blockquote>));
}
<script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>
```

ただ、`entry.data`で必要な情報はすべて手に入るのでCSS完全に理解しているほうはそちらのほうが良いでしょう。私は理解できていません(´；ω；｀)

## Misskey

Misskeyの読み込みにはRSSを使います。

### インストール

```sh
npm install @ascorbic/feed-loader
```

### 設定

先ほどと同じく`src/content.config.ts`を編集しましょう。

```ts
import { defineCollection } from "astro:content";
import { authorFeedLoader } from "@ascorbic/bluesky-loader";

const misskeyFeed = defineCollection({
  loader: feedLoader(
    {url: "https://{instance}/@{handle}.atom"}
  )
})

export const collections = { misskeyFeed };
```

`instance`と`handle`を自身のものに設定しておいてください。

これで先程と同じようにMarkdownと同じノリで使えるようになります。こちらでは自身の投稿の最新20件を取得できます。全て取得したい場合はloaderを作ってください。そして公開してください。使います。

こちらも`entry`を`JSON.stringify()`したものを置いておきます。

:::details 開く。
```json
{
  "id": "https://misskey.io/notes/a20fee59u91o0e9a",
  "data": {
    "title": "New note by ＼ハチミツ／",
    "description": "自宅鯖をもらったからお一人様に移行しようかな",
    "summary": null,
    "date": "2024-12-20T14:44:31.389Z",
    "pubdate": "2024-12-20T14:44:31.389Z",
    "link": "https://misskey.io/notes/a20fee59u91o0e9a",
    "origlink": null,
    "author": "＼ハチミツ／",
    "guid": "https://misskey.io/notes/a20fee59u91o0e9a",
    "comments": null,
    "image": {},
    "categories": [],
    "enclosures": [],
    "meta": {
      "#ns": [
        {
          "xmlns": "http://www.w3.org/2005/Atom"
        }
      ],
      "#type": "atom",
      "#version": "1.0",
      "title": "＼ハチミツ／ (@hatimitu_76@misskey.io)",
      "description": "3355 Notes, 483 Following, 160 Followers · $[border.radius=99999,color=dfdfdf $[bg.color=fff $[fg.color=000   :ablobcat_negi: ボカロ好 き  ]]]\\n:role_computer_suki:\\n:javascript:やら:typescript:やらを勉強して好き勝手にプログラミングしてる\\n無知←ここ重要",
      "date": "2024-12-20T15:35:55.301Z",
      "pubdate": "2024-12-20T15:35:55.301Z",
      "link": "https://misskey.io/@hatimitu_76",
      "xmlurl": "https://misskey.io/@hatimitu_76.atom",
      "author": "＼ハチミツ／",
      "language": null,
      "image": {
        "url": "https://proxy.misskeyusercontent.jp/avatar.webp?url=https%3A%2F%2Fmedia.misskeyusercontent.jp%2Fio%2F0a3e399b-b115-4143-b52b-a1312fefcd66.webp&avatar=1"
      },
      "favicon": null,
      "copyright": "＼ハチミツ／",
      "generator": "Misskey",
      "categories": []
    }
  },
  "rendered": {
    "html": "自宅鯖をもらったからお一人様に移行しようかな"
  },
  "collection": "miioFeed"
}
```
:::

こちらもAPIでウンタラカンタラできそうですが、認証がしなければいけないので面倒ですね。

### 投稿を埋め込む

Misskey本体では実装されていますが、ioにはまだ反映されていないので`entry.data`とCSSでなんとかしましょう。

MFMを実装できる人は実装すると面白いでしょう。

```ts
import { getCollection } from "astro:content";
const misskeyFeed = await getCollection("misskeyFeed")
```

```tsx
{
  misskeyFeed.map(async (mi) => {
    return (<blockquote>
      <a href={mi.data.link}>{mi.description}</a>
    </blockquote>)
  })
}
```
