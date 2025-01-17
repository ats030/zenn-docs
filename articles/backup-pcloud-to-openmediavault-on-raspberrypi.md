---
title: "OpenMediaVaultをインストールしたRaspberry PiでpCloudを自動バックアップする設定"
emoji: "⏲️"
type: "tech"
topics:
  - "pcloud"
  - "openmediavault"
  - "raspberrypi"
  - "crontab"
published: true
published_at: "2025-01-17 06:00"
---

# crontabとは？

crontabは、Unix系オペレーティングシステムで定期的にジョブを実行するための設定ファイルです。ユーザーはこのファイルを編集して、特定の時間や間隔でコマンドやスクリプトを自動的に実行するスケジュールを設定できます。crontabは分、時、日、月、曜日の5つのフィールドで構成され、各フィールドに指定した値に基づいてジョブが実行されます。

# Raspberry Piの設定

以下の手順で、Raspberry PiにOpenMediaVaultをインストールしておきます。

https://zenn.dev/ats030/articles/install-openmediavault-on-raspberrypi-os

# バックアップ方法

## 環境変数HOMEを確認する

Raspberry Pi上で以下のコマンドを実行して、ホームディレクトリの環境変数```HOME```が定義されていることを確認しておきます。

```bash
echo $HOME
```

今回のようにOpenMediaVaultをマウントしたRaspberry Piの場合、以下のように表示されます。

```bash
/srv/dev-disk-by-uuid-<UUID>/nas/<ユーザー名>
```

これがOpenMediaVaultをインストールしたRaspberry Piのホームディレクトリの絶対パスになります。

環境変数```HOME```は、crontabの設定やシェルスクリプトの作成時に使用します。

## crontabの設定ファイルへの記入方法

crontabファイルの末尾に、スペース区切りで以下のフィールドを並べた行を追加することで、スケジューリング設定ができるようになっています。

| フィールド | 内容 |
| ---- | --- |
| 分 | 0〜59までの整数 |
| 時 | 0〜23までの整数 |
| 日 | 1〜31までの整数 |
| 月 | 1〜12までの整数 |
| 曜日 | 0〜6までの整数（0が日曜日） |
| コマンド | 実行するコマンド |

# バックアップの設定例

## シェルスクリプトの設定例

今回は、pCloudのホームディレクトリのバックアップを取得するため、定期実行するシェルスクリプト```$HOME/cron/backup.sh```を以下の内容で作成しました。

```bash
#!/bin/bash

# マウントポイントの確認と既存マウントの解除
MOUNT_POINT="$HOME/pCloudDrive"
if mountpoint -q "$MOUNT_POINT"; then
    echo "既存のマウントを解除します..."
    fusermount -u "$MOUNT_POINT"
fi

# ディレクトリが存在しない場合は作成
if [ ! -d "$MOUNT_POINT" ]; then
    mkdir -p "$MOUNT_POINT"
fi

# pCloudをマウント
rclone mount pcloud: "$MOUNT_POINT" --daemon --cache-dir $HOME/.local/var/rclone --vfs-cache-mode full

# マウントの確認（少し待機）
sleep 5
if ! mountpoint -q "$MOUNT_POINT"; then
    echo "pCloudのマウントに失敗しました。終了します。"
    exit 1
fi

# バックアップ対象のディレクトリ
BACKUP_SOURCE="$MOUNT_POINT"

# バックアップ先のディレクトリ
BACKUP_DEST="$HOME/Backup"

# バックアップファイル名（日本時間で日付を含む）
DATE=$(TZ='Asia/Tokyo' date '+%Y%m%d-%H%M%S')
BACKUP_FILE="backup_pcloud_$DATE.tgz"

# バックアップの実行
if tar -czf "$BACKUP_DEST/$BACKUP_FILE" -C "$BACKUP_SOURCE" .; then
    echo "バックアップが正常に完了しました: $BACKUP_DEST/$BACKUP_FILE"
else
    echo "バックアップ中にエラーが発生しました。"
    exit 1
fi

# 古いバックアップファイルの削除（3回以上前のファイル）
find "$BACKUP_DEST" -name "backup_pcloud_*.tgz" -type f -mtime +3 -delete

# pCloudのアンマウント
fusermount -u "$MOUNT_POINT"
```

pCloudのホームディレクトリを、OpenMediaVaultをマウントしたRaspberry Piの```$HOME/Backup/```に.tgzファイルで保存して、最新の3回分だけ残して古い.tgzファイルは削除するようにしました。

## crontabの設定例

以下のコマンドで、crontabの設定を編集します。

```bash
crontab -e
```

crontabの設定ファイルが開くので、例えば以下のような記述を末尾に追加します。

```Plane text
# pCloudを$HOME/Backupにバックアップする
# （毎週月曜の2:00に$HOME/cron/backup.shを実行する）
0 2 * * 1 $HOME/cron/backup.sh
```

crontabの設定ファイルに編集内容を保存して終了すると、スケジュール設定が反映されます。