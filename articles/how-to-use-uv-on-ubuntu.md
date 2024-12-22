---
title: Ubuntu環境でuvを使ってPythonのパッケージ管理をする
emoji: 💻
type: tech
topics:
  - uv
  - python
  - ubuntu
published: true
published_at: 2024-12-23 07:00
---

# uvとは

https://docs.astral.sh/uv/

uvは、Rustで開発された高速なPythonパッケージおよびプロジェクト管理ツールです。従来は複数のツールで行っていた作業を、uvひとつで統合的に管理できるようになりました。

## 主な機能

### パッケージ管理

- 依存関係の追加、削除、更新が可能
- pipの代替として高速なパッケージインストールを実現

### プロジェクト管理
- pyproject.tomlを使用してプロジェクト情報を一元管理
- 仮想環境の作成と管理

### Pythonバージョン管理
- pyenvのような機能でPythonのバージョンを管理

### uvの利点
- 高速性: Rust製であるため、依存関係の解決やパッケージのインストールが非常に高速
- 統合管理: ランタイムのバージョン管理、仮想環境、パッケージ管理を一つのツールで実現
- 簡素化: 複数のツールを使い分ける必要がなくなり、プロジェクト管理が簡素化
- 再現性: uv.lockファイルにより、クロスプラットフォームでの再現性が向上

# uvをインストールする

Ubuntuの「アプリセンター」から「uv (by Astral)」をインストールするのが簡単です。

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

```bash
uv --version
```

# 基本的な使用方法

## プロジェクトの初期化

https://docs.astral.sh/uv/reference/cli/#uv-init

Pythonプロジェクトを初期化するためには、プロジェクトフォルダで以下のコマンドを実行します。

```bash
uv init <プロジェクト名>
```

## 依存関係の追加

https://docs.astral.sh/uv/reference/cli/#uv-add

Pythonプロジェクトに依存パッケージを追加するには、以下のコマンドを実行します。

```bash
uv add <パッケージ名>
```

## 依存関係の削除

https://docs.astral.sh/uv/reference/cli/#uv-remove

Pythonプロジェクトから依存パッケージを削除するには、以下のコマンドを実行します。

```bash
uv remove <パッケージ名>
```

## 環境の同期

https://docs.astral.sh/uv/reference/cli/#uv-sync

Pythonプロジェクトの依存関係を管理し、環境を最新の状態に同期するには、以下のコマンドを実行します。

```bash
uv sync
```

## Pythonスクリプトの実行

https://docs.astral.sh/uv/reference/cli/#uv-run

Pythonプロジェクト内でスクリプトやコマンドを実行するには、以下のコマンドを実行します。

```bash
uv run <スクリプト名.py>
```