---
title: "TailscaleでLinux端末をVPN接続する"
emoji: "🌐"
type: "tech"
topics:
  - "linux"
  - "vpn"
  - "tailscale"
published: true
published_at: "2024-12-09 23:19"
---

# Tailscaleとは

https://tailscale.com/

Tailscaleは、簡単に使えるVPN（仮想プライベートネットワーク）サービスです。デバイス間でファイル共有したり、外出先から自宅のコンピューターにアクセスしたりするのに便利です。

# cURLのインストール

curlコマンドが使用できない場合、以下の記事を参考にインストールします。

https://zenn.dev/ats030/articles/719b462067397b

# Tailscaleのインストール

curlコマンドで以下のようにインストールします。

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

# Tailscale用のIPアドレスを発行する

以下のコマンドを実行して、表示されるURLを開いてログインすると、Linux端末がTailscaleに接続されます。

```bash
sudo tailscale up
```

これで、接続した端末のTailscale用のIPアドレスが発行されました。Tailscaleにログインするなどして確認できます。

Tailscale用のIPアドレスを用いて、他のTailscaleに接続した端末との間で、外出先でもローカルネットワークと同様にSSH接続やVNC接続などができるようになりました。