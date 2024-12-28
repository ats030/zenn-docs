---
title: Ubuntu環境でGear Leverを設定する
emoji: ⚙️
type: tech
topics:
  - gearlever
  - appimage
  - ubuntu
published: true
published_at: 2024-12-28 21:00
---

# Gear Leverとは

https://mijorus.it/projects/gearlever/

Gear Leverは、AppImageファイルをUbuntuのサイドバーに簡単に登録できるアプリです。AppImageアプリを素早く起動したり、ピン留めしたりすることができ、アプリの管理が非常に簡単になります。

# Gear Leverの初期設定

## FUSEの設定

https://github.com/AppImage/AppImageKit/wiki/FUSE

執筆時点での Ubuntuの最新LTS（24.04）では、AppImageファイルを実行するために、以下のコマンドでFUSE（Filesystem in Userspace）をセットアップしておく必要があります。

```bash
sudo add-apt-repository universe
sudo apt install libfuse2t64
```

## Gear Leverのインストール

今回はFlatpakでGear Leverをインストールします。サンドボックス化されているのでライブラリの依存性などから開放されるのでおすすめです。

https://zenn.dev/ats030/articles/install-flatpak-on-ubuntu