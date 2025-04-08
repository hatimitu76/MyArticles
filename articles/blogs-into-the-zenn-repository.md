---
title: "個人ブログの記事をgit submoduleを使ってZennのリポジトリで統合管理する"
emoji: "🧑‍🤝‍🧑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["zenn", "markdown", "git", "githubactions", "Astro"]
published: true
---

サイト自身と記事が同じリポジトリで管理されていることが気持ち悪くなったので、Zennの記事リポジトリにブログの記事を突っ込みました。

## 対象読者

+ Astro Content Collectionを使ってブログを作成している方
+ ZennをGithub連携している方

## 使用したもの

+ git submodule
+ Astro
+ zenn-markdown-html・zenn-content-css

## 概要

Zennの記事リポジトリにブログの記事を移行し、それをsubmoduleとしてブログリポジトリへ取り込みます。

:::message

関係のないものは省略しています。

:::

### 移行前の状態

```txt:ブログレポジトリ
📂public
  📂images // ここに画像などを収納
📂src
  📂contents
    📂blog // ここにブログのMarkdownを収納
  📝content.config.ts // ./src/contents/blog/ = ブログのディレクトリ を定義
  📂layouts
  📂pages
    📂blog
      📝[...slug].astro // ブログをAstro標準機能を使ってレンダリング
      📝index.astro // ブログの一覧
📝astro.config.mjs
📝.pages.yaml
```

```txt:Zennの記事レポジトリ(MyAritcles)
📂articles
📂book
📂images
```

### 移行後の状態

```diff:ブログレポジトリ
 📂public
-  📂images
+📂MyAritcles // Zennに連携された記事レポジトリ
 📂src
-  📂contents
-    📂blog
-  📝content.config.ts // ./src/contents/blog/ = ブログのディレクトリ を定義
+  📝content.config.ts // ./MyAricles/blog/ = ブログのディレクトリ を定義
   📂layouts
   📂pages
     📂blog
-      📝[...slug].astro // ブログをAstro標準機能を使ってレンダリング
+      📝[...slug].astro // ブログをzenn-markdown-htmlを使ってレンダリング
       📝index.astro // ブログの一覧
+    📂techBlog
+      📝[...slug].astro // Zennの記事をzenn-markdown-htmlを使ってレンダリング
+      📝index.astro // Zennの記事の一覧    
 📝astro.config.mjs
 📝.pages.yaml
```

```diff:Zennの記事レポジトリ(MyAritcles)
 📂articles
 📂book
+📂blog
+📂blogImages
 📂images
```

## 詳細

いくつかの手順に分けて説明します。

1. ブログの記事と画像をZennのリポジトリに移行する
2. Zennのリポジトリをsubmoduleとして追加する
3. 記事を表示できるように調整する
4. Zennのリポジトリにpushされたとき、submoduleを更新するGithub Actionsを書く

### 1.ブログの記事と画像をZennのリポジトリに移行する

自身のブログで使う画像は`images`ではない別のディレクトリで管理しましょう。こうすることでZennへ画像がアップロードされなくなります。

そしてこのときフロントマターも合わせてしまいましょう。思考が楽になります。

```diff:Zennの記事レポジトリ(MyAritcles)
 📂articles
 📂book
+📂blog
+📂blogImages
 📂images
```

### 2.Zennのリポジトリをsubmoduleとして追加する

git submoduleを使ってZennの記事リポジトリを追加します。

git submoduleとは、Gitリポジトリの中に別のGitリポジトリを持つことができる機能です。これを使うことで、Zennの記事リポジトリをブログのリポジトリに追加できます。

このコマンドでZennの記事リポジトリを追加します。

```sh
# repository_urlはZennの記事リポジトリのURL
git submodule add $repository_url
```

これをこのまま実行すると、親リポジトリのルートに記事リポジトリが生成されます。

好きな場所に生成するためには、`git submodule add`の後に追加したいディレクトリを指定します。

```sh
git submodule add $repository_url $directory_name
```

### 3.記事を表示できるように調整する

#### 3-1.ContentCollentionの設定

`content.config.ts`を修正して記事を扱いやすくします。

このとき、Zennの記事も表示できるようにしてしまいましょう。

```ts:./src/content.config.ts
import { defineCollection, z } from "astro:content";
import { glob } from "astro/loaders";

// フロントマターの形式は同じなのでここで定義してしまいます。
const zennStyleFrontMatter = z.object({
	title: z.string(),
	emoji: z.string(),
	type: z.string(),
	topics: z.array(z.string()).optional(),
	published: z.boolean(),
});

// パスの部分は各々のリポジトリに合わせてください。
const blog = defineCollection({
	loader: glob({ base: "./MyArticles/blog", pattern: "**/*.{md,mdx}" }),
	schema: zennStyleFrontMatter,
});

const techBlog = defineCollection({
	loader: glob({ base: "./MyArticles/articles", pattern: "**/*.{md,mdx}" }),
	schema: zennStyleFrontMatter,
});

// exportは忘れずに
export const collections = { blog, techBlog };
```

#### 3-2.zenn-markdown-htmlの準備

Astroだとzenn-markdown-htmlが動かないので以下の記事を参考にして動くようにします。

https://zenn.dev/rorisutarou/articles/ec3871ec55693d

```ts:./src/util.ts
import zennMarkdown from "zenn-markdown-html";

export function markdownToHtml(markdown: string) {
  // @ts-ignore
  const markdownToHtml = zennMarkdown.default ? zennMarkdown.default : zennMarkdown;
  const content = markdownToHtml(markdown)
  return content;
}
```

#### 3-3.記事の表示

修正したzenn-markdown-htmlを使ってMarkdownをHTMLに変換しましょう。

`src/pages/blog/[...slug].astro`を以下のように修正します。

```ts:./src/pages/blog/[...slug].astro
import { type CollectionEntry, getCollection } from "astro:content";
import { markdownToHtml } from "../../util";
import "zenn-content-css";

export async function getStaticPaths() {
	const posts = await getCollection("blog");
	return posts.map((post) => ({
		params: { slug: post.id },
		props: post,
	}));
}

const html = markdownToHtml(
	post.body ?? "Error: No content\n管理者に連絡してください。",
);

```

```html:./src/pages/blog/[...slug].astro
<article class="znc" set:html={html}></article>
```

Zennの記事の表示も9割コピペでできます。`getStaticPaths`の中にある`getCollection`の部分を`techBlog`に変えるだけです。

一覧は`getCollection("techBlog")`を使って実装してください。

#### 3-4.画像の表示

このままでは画像が表示されません。
記事リポジトリの記事は`images`または`blogImages`以下の画像を直接参照しているので、画像を取得して表示するAPIエンドポイントを作成する必要があります。

`pages`以下に画像を取得して表示するためのAPIエンドポイントを作成します。

このとき画像のMIMEタイプは自動で取得できないので**mime-types**を使用します。ブラウザは拡張子ではなくMIMEタイプでファイル種別を判断するので、必ず設定してください。

```sh
npm install mime-types
pnpm add mime-types
bun add mime-types
```

`./src/pages/images/[id].ts`を作成して以下のように記述します。

```ts:./src/pages/images/[id].ts
import fs from "node:fs";
import type { APIContext } from "astro";
import mime from "mime-types";

// IMAGE_DIR は各自のディレクトリに合わせてください。
const IMAGE_DIR = "./MyArticles/images";

export async function getStaticPaths() {
	const images = fs.readdirSync(IMAGE_DIR);
	return images.map((image) => ({
		params: { id: image },
		props: { image: image },
	}));
}

export async function GET(c: APIContext) {
	const imageName = c.props.image as string;
	const image = fs.readFileSync(`./MyArticles/images/${imageName}`);
	return new Response(image, {
		headers: {
      // Content-Typeは必ず設定してください。
			"Content-Type": mime.lookup(imageName) || "application/octet-stream",
		},
	});
}
```

`./src/pages/blogImages/[id].ts`はコピーしてIMAGE_DIRを`./MyArticles/blogImages`に変更してください。

### 4. 記事リポジトリにpushされたとき、submoduleを更新するGithub Actionsを書く

これはWebhookを使って実装します。

記事リポジトリのmainブランチにpushされたときWebhookが発火して、ブログリポジトリでsubmoduleが更新されるようにします。
  
```yaml:記事リポジトリ：.github/workflows/update-submodule.yml
name: webhook
on:
  push:
    branches:
      - main
jobs:
  webhook:
    runs-on: ubuntu-latest
    steps:
      - name: Send Webhook
        run: |
          curl -L \
          -X POST \
          -H "Accept: application/vnd.github+json" \
          -H "Authorization: Bearer ${{ secrets.BLOGREPO_WEBHOOK_TOKEN }}" \
          -d '{"event_type":"update-myarticles-module"' \
          https://api.github.com/repos/hatimitu76/myblog/dispatches
```

curlでWebhookを叩いています。

```yaml:ブログリポジトリ：./.github/workflows/update-submodule.yml
name: Update Submodule on WebHook

on:
  repository_dispatch:
    types: [update-myarticles-module]

jobs:
  update-submodule:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout prod branch
        uses: actions/checkout@v4
        with:
          ref: prod
          submodules: true
          token: ${{ secrets.ACTIONS_TOKEN }}

      - name: Update Submodule
        run: |
          git config user.name "actions-user"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git submodule update --remote
          git add .
          git commit -m "Update submodule"
          git push origin prod
```

submoduleを更新してコミットしています。

私は開発ブランチと本番ブランチを分けているのでprodブランチを指定していますが、必要に応じて変更してください。

`BLOGREPO_WEBHOOK_TOKEN`と`ACTIONS_TOKEN`はそれぞれ記事リポジトリとブログリポジトリのSecretsに登録しておいてください。

両方とも`Contents`を`Read and Write`に設定したトークンを使ってください。

トークンはGithubのSettingsから作成できます。

https://github.com/settings/personal-access-tokens

トークンは以下のリンクから登録できます。
ttps://github.com/<USER>/<REPO>/settings/secrets/actions

リンク先のRepository secretsから設定してください。
