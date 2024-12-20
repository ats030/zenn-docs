---
title: "Ubuntu PCにFlatpakをインストールする"
emoji: "🌐"
type: "tech"
topics:
  - "flatpak"
  - "ubuntu"
published: true
published_at: "2024-12-19 20:30"
---

# Flatpakとは

https://flatpak.org/

Flatpakは、Linuxデスクトップ向けのソフトウェアデプロイメント・パッケージ管理・アプリケーション仮想化を行うユーティリティソフトウェアです。以下がFlatpakの主な特徴です。

- サンドボックス機能：ユーザーがアプリケーションとシステムを分離して実行できる環境を提供します。
- ディストリビューション非依存：36種類以上のLinuxディストリビューションに対応しており、異なるディストリビューション間でアプリケーションを利用できます。
- 依存関係の解決：独自のランタイムを使用することで、システム全体のライブラリに依存しない設計となっています。
- デスクトップアプリケーション専用：主にデスクトップアプリケーションの配布と実行に特化しています。
- リモートリポジトリ：Flathubなどのリモートリポジトリからアプリケーションをインストールできます。

Flatpakを使用することで、ユーザーは依存関係を気にすることなく、様々なアプリケーションを簡単にインストールして利用できるようになります。これにより、Linuxの利用がより簡単になり、初心者にとってのハードルが下がることが期待されています。

# Flatpakのインストール

https://flatpak.org/setup/Ubuntu

Ubuntu PCにFlatpakをインストールするには、以下のコマンドを実行します。

```bash
sudo apt install flatpak
```

Flatpakアプリをインストールするには、以下のコマンドを実行します。

```bash
sudo apt install gnome-software-plugin-flatpak
```

# Flathubを有効化する

https://flathub.org/

Flatpakでアプリを入手するには、Flathubを有効にします。

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```