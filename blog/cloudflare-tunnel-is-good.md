---
title: Cloudflare Tunnelはいいぞ
emoji: "🚇️"
type: "blog"
topics: ["cloudflare", "cloudflareTunnel"]
published: true
---

---

**注意:** 本記事では、「Cloudflare Tunnelはいいぞ」という話に絞ります。具体的な設定方法については、以下のリンクをご参照ください。

- [Cloudflare Tunnelの使い方解説 (takajun)](https://zenn.dev/takajun/articles/fbd783e459c722)  
- [Cloudflare Tunnelの解説 (Jij Inc.)](https://zenn.dev/jij_inc/articles/659fe35813b940)  

---

# Cloudflare Tunnelのメリット  

## 1. 固定IPが不要  

Cloudflare Tunnelでは、サーバーへの直接接続の代わりにCloudflareを介して通信します。このため、固定IPを契約する必要がありません。通信の流れは以下のようになります：

```
サーバー <---> Cloudflare <---> クライアント
```

これにより、固定IP契約のコストを削減できます。

## 2. Cloudflare Zero Trustで認証を強化  

**Cloudflare Zero Trust**とは、Cloudflareを経由した通信に認証を追加できる便利なサービスです。この機能をCloudflare Tunnelにも適用可能です。  

- メールアドレスに認証コードを送信  
- GoogleアカウントやGitHubアカウントを使用した認証  

簡単かつ安全にアクセス制限を設定できます。

## 3. ブラウザでSSHが可能  

現在はベータ版ですが、ブラウザから直接サーバーにSSH接続できます。  

<figure class="figure-image figure-image-fotolife" title="Cloudflare TunnelでブラウザからSSH接続">[f:id:hatimitu_bin:20241206174953j:plain]<figcaption>Cloudflare Tunnelを使用してブラウザからSSH接続している様子</figcaption></figure>  

これにより、タブレットやChromebookでもSSH操作が可能になり、コンソール環境が制限される場面での利便性が向上します。

ただ、MacやWindowsユーザーはターミナルを使って接続する方が楽なので、この機能は「面白さ特化」かもしれませんね。

# Cloudflare Tunnelのデメリット  

## 1. ドメインが必要  

Cloudflare Tunnelを利用するにはドメインが必要です。費用は年間約1,400円程度ですが、独自ドメインはサイトの公開やBlueskyでの自己証明など幅広く活用でき、固定IPよりもはるかに汎用性があります。

## 2. 特定の通信にはクライアントアプリが必要 (HTTP/HTTPSを除く)  

SSHなどのブラウザを介さない通信では、**cloudflared**というアプリでプロキシする必要があります。この点を事前に理解しておく必要があります。

# まとめ  

**Cloudflare Tunnel**は、固定IP不要でサーバーを公開できる非常にいいサービスです。独自ドメインを契約し、その便利さをぜひ体験してみてください！