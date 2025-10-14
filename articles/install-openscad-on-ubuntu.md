---
title: "UbuntuにOpenSCADをインストールする"
emoji: "📐"
type: "tech"
topics:
  - "openscad"
  - "ubuntu"
published: true
published_at: "2025-10-14 00:00"
---

# UbuntuにOpenSCADをインストールする

## 概要
**OpenSCAD** は、3Dモデリングをプログラミングで行うためのオープンソースソフトウェアです。  
GUIで形を直接操作するのではなく、**スクリプト（コード）で3Dモデルを定義**することが特徴です。  
Ubuntu上で簡単にインストール・実行できます。

---

## 特徴

### メリット
- **コードベースの3D設計**が可能（再現性・自動化・パラメータ化が容易）
- STL形式などの3Dプリント用ファイル出力に対応
- Linux / macOS / Windows で利用可能（クロスプラットフォーム）
- コマンドラインからの**バッチ生成**や**スクリプト自動化**が可能

### デメリット
- GUIでの直感的なモデリングは苦手
- 曲面などの複雑形状はスクリプトで表現しづらい
- OpenGL依存のため、環境によって描画に問題が出ることがある

---

## 実装例

### ✅ Ubuntuへのインストール方法

```bash
# システムを更新
sudo apt update

# OpenSCADをインストール
sudo apt install openscad -y
```

または、最新バージョンを使いたい場合はSnapを利用します：

```bash
sudo snap install openscad
```

---

### ✅ シンプルなOpenSCADスクリプト例

以下のコードは「10mmの立方体と直径5mmの円柱」を組み合わせた3Dモデルを作成します。

```scad
// example.scad

// 立方体を定義
cube([10, 10, 10]);

// 円柱を重ねて配置
translate([5, 5, 10])
cylinder(h=10, r=2.5);
```

#### 実行方法

ターミナルから次のコマンドでレンダリングまたはSTL出力が可能です。

```bash
# GUIで開く
openscad example.scad

# STLファイルを出力（自動化用）
openscad -o output.stl example.scad
```

---

## 参考リンク

* 🔗 [OpenSCAD 公式サイト](https://openscad.org/)
* 🔗 [OpenSCAD User Manual（公式ドキュメント）](https://en.wikibooks.org/wiki/OpenSCAD_User_Manual)
* 🔗 [Ubuntu Packages - OpenSCAD](https://packages.ubuntu.com/search?keywords=openscad)
* 🔗 [GitHub - OpenSCAD Repository](https://github.com/openscad/openscad)

