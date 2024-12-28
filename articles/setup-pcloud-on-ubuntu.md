---
title: Ubuntu環境でpCloudを設定する
emoji: ☁️
type: tech
topics:
  - pcloud
  - rclone
  - systemd
  - ubuntu
published: true
published_at: 2024-12-28 22:00
---

# pCloudクラウドストレージの設定

## pCloudとは

https://www.pcloud.com/

pCloudは、スイス発のクラウドストレージサービスで、高いセキュリティとプライバシー保護を特徴としています。ユーザーは一度の支払いで生涯利用できる買い切りプランを選択でき、追加料金が発生しない点が魅力です。

## pCloudのアカウントを作成する

以下を参考にするなどして、pCloudのアカウントを作成しておいてください。

https://www.pcloud.com/ja/help/general-help-center/how-do-i-create-a-pcloud-account

## pCloud Appの設定

:::message
pCloud AppはUbuntuを起動するたびにpCloudアカウントに手動ログインする必要があります。（後述のように）Ubuntu起動時のpCloudのマウントを自動化したい場合には、pCloud Appの設定は不要です。
:::

pCloud AppのAppImageファイルを実行するため、事前にGear Leverをインストールしておきます。

https://zenn.dev/ats030/articles/setup-gearlever-on-ubuntu

pCloud Appは、デスクトップアプリとして仮想ドライブのように機能します。これにより、PCのディスクスペースをほとんど使用せずにファイルを管理でき、簡単にアップロードやダウンロードが可能になります。

以下から```pcloud```（pCloud AppのAppImageファイル）を任意のディレクトリにダウンロードします。

https://www.pcloud.com/ja/download-free-online-cloud-file-storage.html

Gear Leverから```pcloud```を開いて、アプリを立ち上げます。

pCloudのアカウントを選択するよう要求されますので、指示に従って設定を進めます。正しく設定されるとアプリが立ち上がって、```~/pCloudDrive/```ディレクトリ内にpCloudがマウントされます。

# Rcloneによるマウント設定

pCloudのマウントをCLIで実行するにはRcloneを使用するのが良さそうです。

## Rcloneとは

https://rclone.org/

Rcloneは、多様なクラウドストレージサービス間でファイルを管理・同期するためのオープンソースのコマンドラインツールです。pCloud以外にも、Google DriveやDropboxなど40以上のクラウドストレージに対応し、ファイルのアップロード、ダウンロード、同期が簡単に行えます。高速で効率的なファイル転送が可能で、大容量データの移行にも適しています。

## Rcloneのインストール

以下のコマンドでRcloneをインストールします。

```bash
sudo apt install rclone
```

## RcloneにpCloudを登録する

以下のコマンドでRcloneにpCloudのリポジトリを登録します。

```bash
rclone config
```
以後、以下のように設定していきます。

```bash
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q>
```

```n```を入力して、新しい接続先を設定します。

```bash
Enter name for new remote.
name>
```

```pcloud```を新しい接続先名に設定します。

```bash
Choose a number from below, or type in your own value.
...
33 / Pcloud
   \ (pcloud)
...
Storage>
```

接続先のストレージとしてpCloudを選択します。今回は```33```を入力しました。

```bash
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_id>
```

空欄のまま```Enter```を入力します。

```bash
Leave blank normally.
Enter a value. Press Enter to leave empty.
client_secret>
```

空欄のまま```Enter```を入力します。

```bash
Edit advanced config?
y) Yes
n) No (default)
y/n>
```

```n```と入力するか、空欄のまま```Enter```を入力します。

```bash
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine

y) Yes (default)
n) No
y/n>
```

```y```と入力するか、空欄のまま```Enter```を入力します。

ブラウザが立ち上がって、ログイン画面が表示されます。

正しくログインが行われると、コンソール上で以下の質問が続けられます。

```bash
Keep this "pcloud" remote?

y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d>
```

```y```と入力するか、空欄のまま```Enter```を入力します。

```bash
Current remotes:

Name                 Type
====                 ====
pcloud               pcloud

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q>
```

```q```を入力して環境から出ます。

## Rcloneのテスト

以下のコマンドで、RcloudからpCloudの情報を取得してみます。

```bash
rclone lsd pcloud:
```

pCloudのホームディレクトリ内のディレクトリの一覧が表示されれば、登録は正しく行われています。

## pCloudのマウントテスト

（pCloud Appのほうで生成されるディレクトリと同じ）```~/pCloudDrive/```ディレクトリを作って、そこにRcloneの```mount```コマンドでpCloudをマウントするようにします。

```bash
sudo mkdir ~/pCloudDrive/
sudo chmod +x ~/pCloudDrive/
rclone mount pcloud: ~/pCloudDrive/ --daemon --cache-dir ~/.local/var/rclone/ --vfs-cache-mode full
```

```~/pCloudDrive/```ディレクトリを開くと、RcloneによってpCloudのホームディレクトリにアクセスできるようになっていることが確認できます。

```/usr/sbin/```内の```mount```コマンドは、```-t FSTYPE```オプション毎に```/bin/mount.FSTYPE```が呼び出されるようになっています。 そこで、```rclone```について、以下のシンボリックリンクを張ることで、 ```mount```コマンドで```rclone```を呼び出すことが出来るようになります。

```bash
sudo rm -r /sbin/mount.rclone
sudo ln -s /usr/bin/rclone /sbin/mount.rclone
```

シンボリックリンクが貼れたら、```mount```コマンドでマウントしてみます。

```bash
mount pcloud: ~/pCloudDrive/ -t rclone -o cache-dir="~/.local/var/rclone/",vfs-cache-mode=full
```

以上の様に、```rclone mount```コマンドは、``` mount```コマンドで置き換える事ができるようになりました。 つまり、システムは、```mount```コマンドを使って、 他のファイルシステムと同様にpCloudをマウント出来るようになったということです。

# systemdによる自動マウント設定

Ubuntu起動時に```mount```コマンドが実行されてpCloudがマウントされるようにするため、systemdを使用します。

## systemdとは

https://systemd.io/

systemdは、Linuxシステムの起動と管理を担う現代的なソフトウェアです。従来のinitシステムに代わり、高速な起動や柔軟なプロセス管理を実現し、多くの主要Linuxディストリビューションで採用されています。systemdは「ユニット」と呼ばれる単位でサービスを管理し、設定ファイルを通じて統一的なシステム管理を可能にします。

## systemdのユニットファイル

systemdでは、各設定の単位を「ユニット」と呼ぶことになっています。ユーザー用のユニットファイルは、```~/.config/systemd/user/```ディレクトリ以下に配置します。

ユニットファイルは、そのファイル名がマウントポイントのパスを表します。今回の例では「/home/<ユーザー名>/pCloudDrive/」がマウントポイントになります。そのため、ユニットファイルを以下のようなコマンドで作成して編集します。

```bash
sudo nano ~/.config/systemd/user/home-<ユーザー名>-pCloudDrive.mount
```

このファイルの中身は以下の内容を入力します。

```text
[Unit]
After=network-online.target
[Mount]
Type=rclone
What=pcloud:
Where=/home/<ユーザー名>/pCloudDrive/
Options=vfs-cache-mode=full,cache-dir=/home/<ユーザー名>/.local/var/rclone/
[Install]
WantedBy=default.target
```

## systemdのユニットの起動と終了のテスト

手動でのユニットの起動は、```systemctl```コマンドで行います。

```bash
systemctl --user start /home/<ユーザー名>/pCloudDrive/
```

また、手動でのユニットの終了は以下のコマンドで行います。

```bash
systemctl --user stop /home/<ユーザー名>/pCloudDrive/
```

## ユニットの自動起動

Ubuntu起動時に自動でマウントしてくれるようにするため、以下のコマンドを実行しておきます。

```bash
systemctl --user enable home-<ユーザー名>-pCloudDrive.mount
```

その後、以下のコマンドでUbuntuを再起動します。

```bash
sudo reboot
```

再起動後、pCloudが```~/pCloudDrive/```フォルダに自動でマウントされているはずです。

## ユニットの実行状況の確認と停止

また、ユニットの状況については、```systemctl status```コマンドで確認できます。

```bash
systemctl --user status /home/<ユーザー名>/pCloudDrive/
```

自動マウントを停止したい場合は、```systemctl disable```コマンドを使用します。

```bash
systemctl --user disable home-<ユーザー名>-pCloudDrive.mount
```

# 参考

本記事は、以下の記事を参考にさせて頂きました。

https://neko-mac.blogspot.com/2022/03/rclone.html

https://neko-mac.blogspot.com/2022/03/rclone_22.html