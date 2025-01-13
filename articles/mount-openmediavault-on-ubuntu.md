---
title: "OpenMediaVaultで構築したNASをUbuntu PCにマウントする"
emoji: "💽"
type: "tech"
topics:
  - "openmediavault"
  - "nas"
  - "ubuntu"
  - "raspberrypi"
published: true
published_at: "2025-01-14 07:00"
---

# サーバーの設定

## Raspberry Piでの設定

https://zenn.dev/ats030/articles/install-openmediavault-on-raspberrypi-os

# Rcloneによるマウント設定

OpenMediaVaultのマウントをCLIで実行するにはRcloneを使用するのが良さそうです。

## Rcloneのインストール

https://zenn.dev/ats030/articles/install-rclone-on-ubuntu

## RcloneにOpenMediaVaultを登録する

以下のコマンドでRcloneにOpenMediaVaultのリポジトリを登録します。

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

```openmediavault```を新しい接続先名に設定します。

```bash
Choose a number from below, or type in your own value.
...
35 / SMB / CIFS
   \ (smb)
...
Storage>
```

接続先のストレージとしてSMB / CIFSを選択します。今回は```35```を入力しました。

```bash
Option host.
SMB server hostname to connect to.
E.g. "example.com".
Enter a value.
host>
```

<OpenMediaVaultサーバーのIPアドレス>を入力します。

```bash
Option user.
SMB username.
Enter a string value. Press Enter for the default (<ユーザー名>).
user>
```

空欄のまま```Enter```を入力します。

```bash
Option port.
SMB port number.
Enter a signed integer. Press Enter for the default (445).
port>
```

空欄のまま```Enter```を入力します。

```bash
Option pass.
SMB password.
Choose an alternative below. Press Enter for the default (n).
y) Yes, type in my own password
g) Generate random password
n) No, leave this optional password blank (default)
y/g/n>
```

```y```と入力します。

```bash
Enter the password:
password:
Confirm the password:
password:
```

<パスワード>を2回入力します。

```bash
Option domain.
Domain name for NTLM authentication.
Enter a string value. Press Enter for the default (<グループ名>).
domain>
```

空欄のまま```Enter```を入力します。

```bash
Edit advanced config?
y) Yes
n) No (default)
y/n>
```

空欄のまま```Enter```を入力します。

```bash
Configuration complete.
Options:
- type: sub
- host: <OpenMediaVaultサーバーのIPアドレス>
- pass: *** ENCRYPTED ***
Keep this "openmediavault" remote?
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d>
```

問題なければ、空欄のまま```Enter```を入力します。

```bash
Current remotes:

Name                 Type
====                 ====
openmediavault       smb

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

「ファイル」→「他の場所」で、「サーバーアドレスを入力...」に以下を入力して```Enter```。

```Plane text
smb://<OpenMediaVaultサーバーのIPアドレス>/<共有フォルダ名>
```



以下のコマンドで、RcloudからOpenMediaVaultの情報を取得してみます。

```bash
rclone lsd openmediavault:
```

OpenMediaVaultのホームディレクトリ内のディレクトリの一覧が表示されれば、登録は正しく行われています。

## OpenMediaVaultのマウントテスト

```~/OpenMediaVault/```ディレクトリを作って、そこにRcloneの```mount```コマンドでpCloudをマウントするようにします。

```bash
sudo mkdir ~/OpenMediaVault/
sudo chmod +x ~/OpenMediaVault/
sudo chown <ユーザー名>:<グループ名> ~/OpenMediaVault/
rclone mount openmediavault: ~/OpenMediaVault/ --daemon --cache-dir ~/.local/var/rclone/ --vfs-cache-mode full
```

```~/OpenMediaVault/```ディレクトリを開くと、RcloneによってOpenMediaVaultにアクセスできるようになっていることが確認できます。

```/usr/sbin/```内の```mount```コマンドは、```-t FSTYPE```オプション毎に```/bin/mount.FSTYPE```が呼び出されるようになっています。 そこで、```rclone```について、以下のシンボリックリンクを張ることで、 ```mount```コマンドで```rclone```を呼び出すことが出来るようになります。

```bash
sudo rm -r /sbin/mount.rclone
sudo ln -s /usr/bin/rclone /sbin/mount.rclone
```

シンボリックリンクが貼れたら、```mount```コマンドでマウントしてみます。

```bash
mount openmediavault: ~/OpenMediaVault/ -t rclone -o cache-dir="/home/<ユーザー名>/.local/var/rclone/",vfs-cache-mode=full
```

以上の様に、```rclone mount```コマンドは、``` mount```コマンドで置き換える事ができるようになりました。 つまり、システムは、```mount```コマンドを使って、 他のファイルシステムと同様にOpenMediaVaultをマウント出来るようになったということです。

# systemdによる自動マウント設定

Ubuntu起動時に```mount```コマンドが実行されてOpenMediaVaultがマウントされるようにするため、systemdを使用します。

## systemdとは

https://systemd.io/

systemdは、Linuxシステムの起動と管理を担うソフトウェアです。従来のinitシステムに代わり、高速な起動や柔軟なプロセス管理を実現し、多くの主要Linuxディストリビューションで採用されています。systemdは「ユニット」と呼ばれる単位でサービスを管理し、設定ファイルを通じて統一的なシステム管理を可能にします。

## systemdのユニットファイル

systemdでは、各設定の単位を「ユニット」と呼ぶことになっています。ユーザー用のユニットファイルは、```~/.config/systemd/user/```ディレクトリ以下に配置します。

ユニットファイルは、そのファイル名がマウントポイントのパスを表します。今回の例では「/home/<ユーザー名>/OpenMediaVault/」がマウントポイントになります。そのため、ユニットファイルを以下のようなコマンドで作成して編集します。

```bash
sudo nano ~/.config/systemd/user/home-<ユーザー名>-OpenMediaVault.mount
```

このファイルの中身は以下の内容を入力します。

```text
[Unit]
After=network-online.target
[Mount]
Type=rclone
What=openmediavault:
Where=/home/<ユーザー名>/OpenMediaVault/
Options=vfs-cache-mode=full,cache-dir=/home/<ユーザー名>/.local/var/rclone/
[Install]
WantedBy=default.target
```

## systemdのユニットの起動と終了のテスト

手動でのユニットの起動は、```systemctl```コマンドで行います。

```bash
systemctl --user start /home/<ユーザー名>/OpenMediaVault/
```

また、手動でのユニットの終了は以下のコマンドで行います。

```bash
systemctl --user stop /home/<ユーザー名>/OpenMediaVault/
```

## ユニットの自動起動

Ubuntu起動時に自動でマウントしてくれるようにするため、以下のコマンドを実行しておきます。

```bash
systemctl --user enable home-<ユーザー名>-OpenMediaVault.mount
```

その後、以下のコマンドでUbuntuを再起動します。

```bash
sudo reboot
```

再起動後、OpenMediaVaultが```~/OpenMediaVault/```フォルダに自動でマウントされているはずです。

## ユニットの実行状況の確認と停止

また、ユニットの状況については、```systemctl status```コマンドで確認できます。

```bash
systemctl --user status /home/<ユーザー名>/OpenMediaVault/
```

自動マウントを停止したい場合は、```systemctl disable```コマンドを使用します。

```bash
systemctl --user disable home-<ユーザー名>-OpenMediaVault.mount
```