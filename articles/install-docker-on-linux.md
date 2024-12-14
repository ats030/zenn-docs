---
title: "Linux環境でDockerをインストールする"
emoji: "🐳"
type: "tech"
topics:
  - "docker"
  - "linux"
  - "curl"
published: true
published_at: "2024-12-08 14:54"
---

# cURLのインストール

curlコマンドが使用できない場合、以下の記事を参考にインストールします。

https://zenn.dev/ats030/articles/install-curl-on-linux

# Dockerのインストール

https://www.docker.com/

curlコマンドで以下のように.shファイルをダウンロードします。

```bash
curl -fsSL https://get.docker.com -o ~/get-docker.sh
```

.shファイルを実行してDockerをインストールします。

```bash
sudo sh ~/get-docker.sh
```

.shファイルは不要になったので削除しておきます。

```bash
sudo rm -r ~/get-docker.sh
```

以下のコマンドで、インストールされたDockerのバージョンを確認することができます。

```bash
docker version
```