---
title: "Raspberry Pi OSにOpenMediaVaultをインストールする"
emoji: "💽"
type: "tech"
topics:
  - "openmediavault"
  - "raspberrypi"
published: true
published_at: "2025-01-14 06:55"
---

# OpenMediaVaultとは

https://www.openmediavault.org/

OpenMediaVault（OMV）は、Debianを基盤とした無料のNAS（ネットワーク接続ストレージ）システムです。主にCIFS（Samba）、FTP、NFSなどのプロトコルに対応し、ソフトウェアRAID機能を提供しています。ユーザーはWebブラウザを介して簡単に管理できるため、家庭や小規模オフィスでのデータ共有に適しています。

# システム構成

今回、OpenMediaVaultをインストールしたのは以下の構成のRaspberry Piです。

- ハードウェア
  - Raspberry Pi 4 Model B / 4GB
- OS
  - Raspberry Pi OS Lite (64-bit)
- 外部ストレージ
  - OS起動用→外付けSSD（240GB）
  - NASストレージ用→外付けSSD（2TB）
- インターネット接続
  - 有線LAN（CAT6A LANケーブル）

# Raspberry Piの設定

## Raspberry Piの起動ディスクの作成

https://www.raspberrypi.com/software/

Raspberry Pi ImagerでRaspberry Pi OSの起動ディスクを作成しますが、注意点は以下の3点です。

- CUI版の「Raspberry Pi OS Lite (64-bit)」を使用。
  - OpenMediaVaultがGUI版のOSに対応していないため。
- Wi-Fiの設定は行わない（Raspberry Piを有線LAN接続）。
  - Wi-Fi接続したい場合は、OpenMediaVaultの設定画面で設定を行います。
- ```SSHを有効化する```をチェックして、```パスワード認証を使う```のほうを選択。

## Raspberry PiのIPアドレスの確認

Raspberry Piのディスプレイを接続して、以下のコマンドでIPアドレスを確認しておきます。

```bash
ifconfig
```

# OpenMediaVaultのインストール

## SSH接続する

PCからRaspberry PiにSSH接続します。

```bash
ssh <Raspberry Piのユーザー名>@<Raspberry PiのIPアドレス>
```

## OpenMediaVaultをインストールする

SSH接続後、rootのログインシェルを起動します。

```bash
sudo -i
```

パッケージの更新します。

```bash
sudo apt update && sudo apt upgrade -y
```

作業ディレクトリの移動します。

```bash
cd /opt/
```

OpenMediaVaultをインストールします。

```bash
wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash
```

インストール完了後、自動的にRaspberry Piが再起動します。

# OpenMediaVaultの設定

## OpenMediaVaultにアクセスする

ブラウザから```<Raspberry PiのIPアドレス>```にアクセスします。

## OpenMediaVaultの設定ページにログインする

ログイン画面が表示されます。ここで入力するログイン情報は以下のようにします。

- ユーザー名：```admin```
- パスワード：```openmediavault```

## OpenMediaVaultの設定

OpenMediaVaultの設定ページでは、以下の設定を行います。

- adminパスワードの変更
  - 画面右上の「ユーザー設定」→「パスワードの変更」。
    - 初期パスワードから変更したい場合はこちらを設定。
- OpenMediaVaultの設定ページの日本語化
  - 画面右上の「ユーザー設定」→「言語」→「日本語」。
- NASユーザの設定
  - 「ユーザー」→「ユーザー」。
    - ```<Raspberry Piのユーザー名>```を選択して「編集」ボタンを押す。
    - 「パスワード」/「パスワードの確認」→```パスワード```。
    - 「グループ」で```_ssh```を追加。
    - 「保存」ボタンを押す。→「保留中の構成変更」で「適用」を押す。
- ストレージの設定
  - あらかじめ、外付けディスクをフォーマットしておく。
  https://zenn.dev/ats030/articles/mount-openmediavault-on-ubuntu
  - Raspberry Piに外付けディスクを接続。
  - 「ストレージ」→「ファイルシステム」。
    - 「ファイルシステムを作成し、マウントします。」→「EXT4」。
    - 「デバイス」→```<接続した外付けディスク>```。
    - ファイルシステムの作成が完了したら「閉じる」ボタンを押す。
    - 「ファイルシステム」→```<接続した外付けディスク>```を選択。
    - 「保存」ボタンを押す。→「保留中の構成変更」で「適用」を押す。
  - 「ストレージ」→「共有フォルダ」→「作成」。
    - 「名前」→```共有フォルダ名```（例えば```nas```）。
    - 「ファイルシステム」→```<接続した外付けディスク>```。
    - 「保存」ボタンを押す。→「保留中の構成変更」で「適用」を押す。
- SMBの設定
  - 「ユーザー」→「設定」。
    - 「有効」にチェック。
    - 「ロケーション」→```<共有フォルダ>```。
    - 「保存」ボタンを押す。→「保留中の構成変更」で「適用」を押す。
  - 「サービス」→「SMB/CIFS」→「設定」。
    - 「/有効」にチェック（必要ならワークグループ名も変更）。
    - 「ホームディレクトリ/有効」にチェック。
    - 「保存」ボタンを押す。
    - 「保留中の構成変更」で「適用」を押す。
  - 「サービス」→「SMB/CIFS」→「共有」。
    - 「共有フォルダ」→```<共有フォルダ>```。
    - 「保存」ボタンを押す。→「保留中の構成変更」で「適用」を押す。



# PCにOpenMediaVaultをマウントする

## Ubuntu PCの場合

https://zenn.dev/ats030/articles/mount-openmediavault-on-ubuntu