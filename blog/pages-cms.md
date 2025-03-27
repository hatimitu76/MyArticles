---
title: Pages CMSでMarkdownをSSGしているサイトにCMSを追加する
emoji: "📝"
type: blog
topics: ["PagesCMS", "Markdown", "SSG"]
---
こんにちは、お元気してますか？　PCを開くのがだるすぎて年末年始に何もしなかったハチミツです。Markdownを使ったSSGはサーバーがいらないことがメリットですが、スマホから編集できないのが欠点ですよね。大幅なモチベ低下を招きます。

そこでご紹介したいのがこちらのPages CMS。今までの構成をそのままに、設定ファイルを追加して連携するだけでリッチなインターフェイスであなたのブログを編集できるようになります。

## Pages CMSの機能

Pages CMSはGithubと連携することでレポジトリにあるファイルを編集するWebアプリです。オープンソースで開発されています。そのためセルフホストも可能ですが、公式でサーバーが建てられています。特に事情がなければ公式鯖を使いましょう。
[https://app.pagescms.org](https://app.pagescms.org)

Pages CMSは一般的なCMSのようにAPIでコンテンツを取得するのではなく、指定したレポジトリのファイルを直接編集します。このため、ローカルファイルに保存する形で作成していたWebサイトととても相性が良いです。

特にAstroのContent Collectionで作成しているサイトととても相性がいいです。まだ自身のサイトを作成していない方はぜひAstroを使ってみてください。Markdownを標準で変換できたり、JSをサイトから取り除いたりできます。State of JSでも結構評価されてました。
[https://astro.build](https://astro.build)

Pages CMSは一般的なCMSのUIをshadcn/uiで作ったような見た目で、Next.jsによって構築されています。当然レスポンシブにも対応していて、レイアウトが微妙だということもありません。完璧です。

リッチエディタと日本語入力の相性はそれほどよくありませんが、一般的なCMSと同じ感覚で編集することができます。しかし、パーサーのプラグインなどで記法を拡張している場合、それを制限することになってしまいます。もちろんMarkdownを直書きする方法も用意されているので拡張している方は試してみてください。

Pages CMSがサポートしている出力ファイル形式は、`yaml-frontmatter`、`json-frontmatter`、`toml-frontmatter`、`yaml`、`json`、`toml`、`datagrid`、`code`そして`raw` です。大半のSSGが使うローカルファイルは揃えられています。デフォルトは`yaml-frontmatter` になっています。

### サポートされているフィールドの種類

+ [boolean](https://pagescms.org/docs/configuration/boolean-field)
+ [code](https://pagescms.org/docs/configuration/code-field)
  + サポートされている言語は`yaml`、`javascript` 、`json`、`html` 、`markdown`
+ [date](https://pagescms.org/docs/configuration/date-field)
  + 設定することで時間まで設定できるようになる
+ [image](https://pagescms.org/docs/configuration/image-field)
+ [number](https://pagescms.org/docs/configuration/number-field)
+ [object](https://pagescms.org/docs/configuration/object-field)
  + フィールドを入れ子にできる
+ [rich-text](https://pagescms.org/docs/configuration/rich-text-field)
+ [select](https://pagescms.org/docs/configuration/select-field)
+ [string](https://pagescms.org/docs/configuration/string-field)
  + 一行テキスト
+ [text](https://pagescms.org/docs/configuration/text-field)
  + 複数行テキスト

一般的なCMSに備えられているようなフィールドが揃っています。

注意してほしいのが、文字列フィールドにユニーク制約をつけれない点です。ファイルで管理するので、スラッグ文字列はファイル名を利用してユニークであることを保証できます。しかし、それ以外の目的でユニーク制約をつけたい場合にデメリットになるかもしれません。

一応リッチエディターはついていますが、拡張記法が制限されてしまうのでコードフィールドで言語をMarkdownにするのがいいでしょう。

## ログイン方法

[app.pagescms.org](https://app.pagescms.org)にアクセスして、**Sign in with Github** をクリックすればいつもの認証画面が出てきて認可すれば完了です。

このとき注意してほしいのが、レポジトリ権限を**すべてのレポジトリ**に設定しなければいけないことです。アプリにはレポジトリを追加のようなメニューはありません。もし間違って権限を単一レポジトリで設定してしまった場合は、[github.com/settings/installations](https://github.com/settings/installations)から変更できます。

![Pages CMSのログイン画面](/images/app.pagescms.org-sign-in.jpg)

認可が終わるとあなたのレポジトリの一覧が表示されます。下の方にはあらがじめ設定ファイルが設置されているテンプレートへのリンクがあります。

## 設定ファイルの設置

Pages CMSの設定は`.pages.yml` に書きます。もちろんルート直下です。

書き方は[こちらの公式ドキュメント](https://pagescms.org/docs/configuration/)に書いてあります。

レポジトリ一覧からお好きなレポジトリを開きましょう。そうするとsettingに飛ばされるはずです。こちらにyaml形式で設定を書いていきます。

![Pages CMSの設定ファイル](/images/app.pagescms.org-setting.jpg)

`.pages.yml` には`media` と`content` の項目があります。`media` にはファイル関係の設定を、`content` にはMarkdownコンテンツの設定を記入します。

詳細な設定は公式ドキュメントを見てもらうとして、最低限設定したほうが良い設定を解説します。

### media

これは大半の場合、こちらをコピペするだけで大丈夫です

```yaml
media:
  input: public
  output: /
```

`input` は静的ファイルを保存する場所へのパスを、`output` はWeb上から見えるファイルのパスを設定します。

### content.fields[x].body

フィールドを設定して行く中で重要になるのがこの`body` フィールドです。この`body` フィールドがMarkdownのテキスト部分になります。

リッチエディターを使う場合はこちらの設定をします。

```yaml
content:
  # 省略
  fields:
    - name: body  # ここ重要
      label: Body
      type: rich-text
```

ただ、MDXやプラグインで記法を拡張している場合はそれが制限されてしまうのでこちらの設定はおすすめできません。

こちらの設定を行うとMarkdownエディターが使えるようになります。

```yaml
content:
  # 省略
  fields:
    - name: body
      label: Body
      type: code
      options: 
        language: markdown  # ここ重要
```

`code`typeはその名の通り何らかの言語のコードを記述できるフィールドです。`language`を`markdown`にすることである程度のハイライトをつけつつ縛りなくMarkdownをかけるようになります。

「俺はハイライトなんていらねぇ！」と`type`を`text`にすると日本語入力するときに画面が上下してしまうのでおすすめしません。

また、一から書く場合でも`name` が`body` になっていないと認識されないので注意してください。

## 最後に

speakerdeckにあった個人技術ブログのモチベ維持や、破壊の繰り返しを防ぐにはどうすればいいのか、、、的なスライドからPages CMSを発見しました。(リンク紛失)発表者様ありがとうございます。

もしPages CMSをあなたのサイトに導入できたら、ぜひそれを記事にして投稿してください。検索してわかる通り、日本語の情報が少ないです。みんなで投稿して個人技術ブログを盛り上げて行きましょう！💪
