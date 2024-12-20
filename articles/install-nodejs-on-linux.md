---
title: "Linux環境でNode.jsをインストールする"
emoji: "🧰"
type: "tech"
topics:
  - "nodejs"
  - "nvm"
  - "linux"
published: true
published_at: "2024-12-19 21:10"
---

# Node.jsとは

https://nodejs.org/

Node.jsは、JavaScriptをサーバーサイドで実行するための環境です。従来、JavaScriptはブラウザ上でのみ動作していましたが、Node.jsの登場により、サーバー上でもJavaScriptを使用できるようになりました。

# nvmのインストール

nvmコマンドが使用できない場合は、以下のページを参考にインストールしておきます。

https://zenn.dev/ats030/articles/install-nvm-on-linux

# Node.jsのインストール

以下のコードでNode.jsをインストールします。

```bash
nvm install node
```
