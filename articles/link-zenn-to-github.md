---
title: "ZennのコンテンツをGitHubで管理する"
emoji: "🛠️"
type: "tech"
topics:
  - "zenn"
  - "git"
  - "github"
published: true
published_at: "2024-12-14 22:00"
---

# GitHub連携のための設定



## Zennのコンテンツを編集するディレクトリを作成する

Zennのコンテンツを編集するディレクトリを用意します。今回使用するディレクトリ名は、```zenn-docs```としておきます。

:::message
この時点でディレクトリ内は空でもかまいません。
:::

## GitHubにリポジトリを作成する

GitHubに新しくリポジトリを作成します。今回はリポジトリ名は上記と同じく```zenn-docs```としておきます。

:::message
無料公開する記事や本についてはPrivateリポジトリとして作成しておけば大丈夫ですが、有料の本を作成する場合はPrivateリポジトリにしておいた方が良いかもしれません。
:::

## Zennのダッシュボードから連携する

以下の記事を参考に、ZennのコンテンツをGitHubリポジトリに連携する設定を行っていきます。

https://zenn.dev/zenn/articles/connect-to-github

- Zennにログインして、ダッシュボードの[GitHubからのデプロイ](https://zenn.dev/dashboard/deploys)を開きます。
- 「リポジトリを連携」を選んで、リポジトリを選択する画面を表示します。
- 「Only select repositories」にチェックを入れて、連携するリポジトリ（今回は```zenn-docs```）を選びます。

# 最初のPushを行う

## 空コミットを行う

Zennのコンテンツを編集するほうの```zenn-docs```ディレクトリをローカルリポジトリに設定して、空コミットしておきます。コミットメッセージは「first commit」としておきます。

```bash
git init
git commit --allow-empty -m "first commit"
```

## mainブランチを生成してGitHubにPushする

以下のコマンドで、```main```ブランチを生成して、ローカルリポジトリをGitHubの```zenn-docs```リポジトリにPushします。

```bash
git branch -M main
git remote add origin https://github.com/<GitHubユーザー名>/zenn-docs.git
git push -u origin main
```

これで、GitHubの```zenn-docs```リポジトリに空コミットがPushされました。

# ローカルリポジトリでZennのコンテンツを編集する

ローカルリポジトリで、以下の記事のようにZenn CLIを有効にして、Zennのコンテンツを編集できるようにしておきます。

https://zenn.dev/ats030/articles/how-to-use-zenn-cli

:::message
Zennのコンテンツの編集には、VSCodeやObsidianのようなエディタを使用できます。
:::

# 編集後のローカルリポジトリをGitHubにPushする

ローカルリポジトリでZennのコンテンツを編集した後、GitHubにPushしたい場合は以下のコマンドを実行します。<コミットメッセージ>には、編集内容の概要を入力するようにしてください。

```bash
git add .
git commit -m "<コミットメッセージ>"
git push -u origin main
```

これで、GitHubの```zenn-docs```リポジトリにローカルリポジトリの編集内容が反映されました。