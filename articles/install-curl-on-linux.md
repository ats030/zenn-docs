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

cURL（カール）は、さまざまなプロトコルを使用してデータを転送するためのコマンドラインツールです。主にAPIのテストや自動化、スクリプト作成に利用され、HTTPリクエストを簡単に行うことができます。cURLは、GET、POSTなどのHTTPメソッドをサポートし、カスタムヘッダーの設定やデータ送信も可能です。

# cURLのインストール

cURLがインストールされていない場合、```curl```コマンドが使用できません。その場合は以下のようにインストールします。

**Debian系**

```bash
sudo apt update
sudo apt install curl
```

**Red Hat系**

```bash
sudo yum install curl
```

以下のコマンドで、cURLが正常にインストールできているかどうかを確認します。

```bash
curl --version
```