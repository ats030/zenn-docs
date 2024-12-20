---
title: "Linux環境でnvmコマンドを使用できるようにする"
emoji: "🛠️"
type: "tech"
topics:mi
  - "nvm"
  - "linux"
published: true
published_at: "2024-12-19 21:00"
---

# nvmとは

https://github.com/nvm-sh/nvm

nvmはNode Version Managerの略で、Node.jsの複数のバージョンを管理し、切り替えることができるツールです。

# cURLのインストール

curlコマンドが使用できない場合は、以下のページを参考にcURLをインストールしておきます。

https://zenn.dev/ats030/articles/install-curl-on-linux

# nvmのインストール

nvmコマンドが使用できない場合、以下のページを参考に設定します。

https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating

以下のコードでnvmコマンドを有効化します。

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

:::message
執筆時点の最新バージョン0.40.1です。```v0.40.1```の部分は最新のリリースに合わせて修正します。
:::