---
title: "Linux環境でcurlコマンドを使えるようにする"
emoji: "🔗"
type: "tech"
topics:
  - "linux"
  - "curl"
published: true
published_at: "2024-12-09 22:56"
---

# cURLとは

https://curl.se/

cURLはコマンドラインからHTTPリクエストを送信するためのツールです。URLを指定してデータを取得したり、データを送信したりすることができます。

# cURLのインストール

cURLがインストールされていない場合、curlコマンドが使用できません。その場合は以下のようにインストールします。

**Debian系**

```bash
sudo apt update
sudo apt install curl
```

**Redhat系**

```bash
sudo yum install curl
```

これで、curlコマンドが使用できるようになりました。