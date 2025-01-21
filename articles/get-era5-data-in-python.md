---
title: "PythonでERA5のデータを取得するための設定"
emoji: "🌤️"
type: "tech"
topics:
  - "python"
published: true
published_at: "2024-12-15 01:00"
---

# CDS APIの設定

## CDSとは

https://cds.climate.copernicus.eu/

Climate Data Store (CDS)とは、Copernicus Climate Change Service (C3S)によって開発・運営されている、過去、現在、未来の気候データに簡単にアクセスできるプラットフォームです。

## CDSのアカウントを有効にする

https://cds.climate.copernicus.eu/how-to-api

CDSにログインして、「API Token」を更新しておきます。

## .cdsapircを設定する

以下の内容の```.cdsapirc```を作成します。

```
url: https://cds.climate.copernicus.eu/api
key: <API Token>
```

**Windowsの場合**

```~/```に```.cdsapirc```を保存します。

**Linuxの場合**

```$HOME/```に```.cdsapirc```を保存します。

## pipでcdsapiをインストールする

以下のコマンドでcdsapiをインストールします。

```bash
pip install cdsapi
```

# ERA5の設定

## ERA5とは

ERA5は欧州中期気象予報センター（ECMWF）が開発した第5世代の大気再解析データセットです。気候変動の理解や監視を目的として作成された格子点値（GPV）データです。

## ERA5のデータセット

**1940年から現在までの単一レベルのERA5の時間毎データ**
https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=overview

**1940年から現在までの圧力レベルごとのERA5の時間毎データ**
https://cds.climate.copernicus.eu/datasets/reanalysis-era5-pressure-levels?tab=overview

それぞれのページから、「Download」タブ→「Terms of use」（利用規約）を「Accepted」（受け入れ）しておきます。

# データを取得する

## データをダウンロードするPythonスクリプト

https://zenn.dev/ats030/articles/download-era5-data-in-python