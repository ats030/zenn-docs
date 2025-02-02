---
title: Visual Studio CodeのJupyter Notebookをuvの仮想環境で実行する
emoji: 📝
type: tech
topics:
  - uv
  - python
  - jupyter
  - vscode
published: true
published_at: 2025-02-02 11:00
---

# 過去の記事

https://zenn.dev/ats030/articles/how-to-use-uv-on-ubuntu

# 必要なパッケージのインストール

uvの仮想環境でJupter Notebookを実行できるようにするために、```uv init```を実行したディレクトリ上で、以下のコマンドを実行して、uvのプロジェクトの仮想環境に```ipykernel```をインストールします。

```bash
uv add --dev ipykernel
```

# Visual Studio Codeでの設定

Visual Studio Code上で```.iptynb```ファイルを作成して開きます。

右上の```カーネルを選択```もしくは```select kernel```から、```.iptynb```ファイルの実行時に使用するuv仮想環境を指定します。

その際、以下のようなパスを入力します。

```Plane text
<プロジェクトディレクトリの絶対パス>/.venv/bin/python3
```