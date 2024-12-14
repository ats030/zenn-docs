---
title: "Windowsで過去のSSH接続の履歴を削除するコマンド"
emoji: "🗑️"
type: "tech"
topics:
  - "tips"
  - "windows"
  - "ssh"
published: true
published_at: "2024-12-08 07:00"
---

# エラーの内容

SSH接続は以下のコマンドで実行します。

```bash
ssh <ユーザー名>@<ホスト名>
```

このとき、以下のようなエラーが出ることがあります。

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

この場合、以前に同じホスト名でSSH接続をした履歴が、「~/.ssh/known_hosts」に残っていることが原因です。

# 対策コマンド

「~/.ssh/known_hosts」を直接編集して該当箇所を削除しても良いのですが、以下のコマンドで削除するほうが簡単です。

```bash
ssh-keygen -R <ホスト名>
```
