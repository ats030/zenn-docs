---
title: "Zenn CLIを使用してコンテンツを編集する"
emoji: "💻"
type: "tech"
topics:
  - "zenn"
  - "zenn cli"
  - "nodejs"
published: true
published_at: "2024-12-14 23:00"
---

# Zenn CLIとは

https://zenn.dev/zenn/articles/zenn-cli-guide

Zenn CLI（Command Line Interface）は、ZennとGitHubリポジトリを連携させて、ローカル環境で好きなエディターを使ってZennの投稿コンテンツを作成・編集するためのツールです。

# 事前準備

## ZennとGiuHubリポジトリの連携

あらかじめ、下記リンクの手順で、Zennのコンテンツを編集する用のディレクトリにGitのローカルリポジトリを作成し、GitHubにPushしておきます。

https://zenn.dev/ats030/articles/link-zenn-to-github

## Node.jsの設定

下記のリンクからNode.jsをインストールしておきます。

https://nodejs.org/en/

以下のコマンドでNode.jsのバージョンが表示されれば、正しくインストールされています。

```bash
node --version
```

# Zenn CLIの設定

## Zenn CLIのインストール

以下のコマンドでZenn CLIをインストールします。
```bash
npm init --yes
npm install zenn-cli
```

## Zenn CLIのアップデート

Zenn CLIのアップデートは以下のコマンドで行います。

```bash
npm install zenn-cli@latest
```

# Zenn CLIを使用してコンテンツを編集する

## Zenn CLIの初期設定

ローカル環境でZennのコンテンツを編集するためのディレクトリを作成して、そこに移動しておきます。

```bash
cd  <Zennコンテンツ編集ディレクトリ>
```

以下のコマンドでZenn CLIのセットアップを行います。

```bash
npm zen init
```

## コンテンツの内容をプレビューする

以下のコマンドを実行します。

```bash
npx zenn preview
```

表示されたURL（通常は```http://localhost:8000```）から、Zennの編集中のコンテンツの内容をプレビューできます。

## 新しいコンテンツを作成する

以下のコマンドで新しいコンテンツ（記事・本）を作成します。

**新しい記事を作成する**

```bash
npx zenn new:article --slug <追加する記事ID>
```

**新しい本を作成する**
```bash
npx zenn new:book --slug <追加する本ID>
```

:::message
記事IDと本IDは、小文字の半角英数字（a-z0-9）、ハイフン（-）、アンダースコア（_）の12〜50字の組み合わせにする必要があります。
:::

:::message
上記コマンドで、```--slug <追加する記事ID>```や```--slug <追加する本ID>```を省略すると、新しく作成される記事や本が自動で生成されたIDと紐付けされます。ローカルで編集する際の検索性を考えると、記事IDや本IDは明示しておいた方が無難です。
:::