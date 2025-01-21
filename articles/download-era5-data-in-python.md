---
title: "PythonでERA5のデータをダウンロードする"
emoji: "🌤️"
type: "tech"
topics:
  - "python"
published: true
published_at: "2025-01-21 23:00"
---

# 事前設定

## ERA5のデータを取得するための設定

https://zenn.dev/ats030/articles/get-era5-data-in-python

# Ubuntu環境でのPythonスクリプトの例

## 作業ディレクトリの構成

任意の```<作業ディレクトリ>```直下に、下記のように```.py```ファイルを3つ配置します。

```Plane text
<作業ディレクトリ>
  ├─ configure.py
  ├─ get_era5_data.py
  └─ get_era5.py
  
```

## ダウンロード条件の設定

気象データのダウンロード条件を設定します。

例えば、```configure.py```の内容を以下のようにします。

```python
from datetime import date, datetime, timedelta

def configure():
    ################################################################################
    # 設定値
    ################################################################################
    # データ抽出期間
    dt1 = datetime(1940, 1, 1, 0, 0, 0)
    dt2 = datetime.now()
    #dt1 = datetime(2024, 1, 1, 0, 0, 0)
    #dt2 = datetime(2024, 12, 31, 0, 0, 0)

    # 圧力レベル
    lev = 1000

    # ディレクトリ
    dir = '<作業ディレクトリ>'

    return dt1, dt2, lev, dir
```

## 時系列で2次元データをダウンロードするための設定

**1940年から現在までの単一レベルのERA5の時間毎データ**
https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=overview

**1940年から現在までの圧力レベルごとのERA5の時間毎データ**
https://cds.climate.copernicus.eu/datasets/reanalysis-era5-pressure-levels?tab=overview

これらから、各種気象データをダウンロードでするための設定を行います。

```get_era5.py```の内容を以下のようにします。

```python
import cdsapi
import netCDF4
import xarray as xr
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
import os
from datetime import date, datetime, timedelta
from geopy.geocoders import Nominatim
from tqdm import tqdm
import time

import certifi
import ssl
import urllib3

class ERA5():
    ssl_context = ssl.create_default_context(cafile=certifi.where())
    urllib3.util.ssl_.create_urllib3_context = lambda *args, **kwargs: ERA5.ssl_context

    ################################################################################
    # データ抽出期間を補正する関数
    ################################################################################
    def correct_dt(shortname, dt1, dt2, path, files):
        filelist = []
        
        for filename in files:
            if os.path.isfile(os.path.join(path, filename)):
                filelist.append(filename)
        
        # データ取得開始日時
        if len(filelist) == 0:
            start = 'ERA5_'+str(format(dt1.year, '04'))+'-'+str(format(dt1.month, '02'))+'-'+str(format(dt1.day, '02'))+'T'+str(format(dt1.hour, '02'))+'_00_00_'+str(shortname)+'.nc'
        else:
            start = max(filelist)
        str_d1 = str(start[5:9])+str(start[10:12])+str(start[13:15])+str(start[16:18])
        
        # データ取得終了日時
        if datetime.now() - timedelta(7) > dt2:
            end = dt2
        else:
            end = datetime.now() - timedelta(7)
        str_d2 = end.strftime('%Y%m%d%H')
        
        # データ抽出期間を設定
        dt1 = datetime(int(str_d1[:4]), int(str_d1[4:6]), int(str_d1[6:8]), int(str_d1[8:]), 0, 0)
        dt2 = datetime(int(str_d2[:4]), int(str_d2[4:6]), int(str_d2[6:8]), int(str_d2[8:]), 0, 0)

    def correct_dt_single(shortname, dt1, dt2, dir):
        if not os.path.exists(str(dir)+'/nc_'+str(shortname)):
            os.makedirs(str(dir)+'/nc_'+str(shortname))
            
        path = str(dir)+'/nc_'+str(shortname)
        files = os.listdir(path)

        ERA5.correct_dt(shortname, dt1, dt2, path, files)

        return (dt1, dt2, dir)

    def correct_dt_pressure(shortname, lev, dt1, dt2, dir):
        if not os.path.exists(str(dir)+'/nc_'+str(shortname)):
            os.makedirs(str(dir)+'/nc_'+str(shortname))
            
        path = str(dir)+'/nc_'+str(shortname)
        files = os.listdir(path)

        ERA5.correct_dt(shortname, dt1, dt2, path, files)

        return (dt1, dt2, dir)

    ################################################################################
    # 「ERA5 hourly data on single levels from 1940 to present」からデータを抽出する関数
    # https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels
    ################################################################################
    def reanalysis_era5_single_levels(name, shortname, dt1, dt2, dir):
        # データ抽出期間を補正
        period = ERA5.correct_dt_single(shortname, dt1, dt2, dir)
        dt1 = period[0]
        dt2 = period[1]
        print(f'{shortname}: {dt1} - {dt2}')

        dt = dt1

        if not os.path.exists(str(dir)+'/nc_'+str(shortname)):
            os.makedirs(str(dir)+'/nc_'+str(shortname))

        while dt <= dt2:
            print(str(name))
            print(dt)

            ncfile = str(dir)+'/nc_'+str(shortname)+'/ERA5_'+str(format(dt.year, '04'))+'-'+str(format(dt.month, '02'))+'-'+str(format(dt.day, '02'))+'T'+str(format(dt.hour, '02'))+'_00_00_'+str(shortname)+'.nc'
            
            if os.path.isfile(ncfile) is False:
                ERA5.c.retrieve(
                    'reanalysis-era5-single-levels',
                    {
                        'product_type': 'reanalysis',
                        'variable': str(name),
                        'year': str(dt.year),
                        'month': str(dt.month),
                        'day': str(dt.day),
                        'time': str(dt.strftime('%H:%M')),
                        'format': 'netcdf'
                    },
                    str(ncfile))
            else:
                pass

            dt += ERA5.delta

    ################################################################################
    # 「ERA5 hourly data on pressure levels from 1940 to present」からデータを抽出する関数
    # https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-pressure-levels
    ################################################################################
    def reanalysis_era5_pressure_levels(name, shortname, lev, dt1, dt2, dir):
        # データ抽出期間を補正
        period = ERA5.correct_dt_pressure(shortname, lev, dt1, dt2, dir)
        dt1 = period[0]
        dt2 = period[1]
        print(f'{shortname}: {dt1} - {dt2}')
        
        dt = dt1

        if not os.path.exists(str(dir)+'/nc_'+str(shortname)):
            os.makedirs(str(dir)+'/nc_'+str(shortname))

        while dt <= dt2:
            print(str(name))
            print(dt)

            ncfile = str(dir)+'/nc_'+str(shortname)+'/ERA5_'+str(format(dt.year, '04'))+'-'+str(format(dt.month, '02'))+'-'+str(format(dt.day, '02'))+'T'+str(format(dt.hour, '02'))+'_00_00_'+str(shortname)+'.nc'

            if os.path.isfile(ncfile) is False:
                ERA5.c.retrieve(
                    'reanalysis-era5-pressure-levels',
                    {
                        'product_type': 'reanalysis',
                        'variable': str(name),
                        'year': str(dt.year),
                        'month': str(dt.month),
                        'day': str(dt.day),
                        'time': str(dt.strftime('%H:%M')),
                        'format': 'netcdf'
                    },
                    str(ncfile))
            else:
                pass
                
            dt += ERA5.delta

    ################################################################################
    # 変数
    ################################################################################
    # 時間間隔
    delta = timedelta(hours=1)

    # CDS API
    c = cdsapi.Client()
```

## 各パラメータのデータをダウンロードする設定

今回は、地表付近の気温、相対湿度、気圧、風速、降水量、全天日射量について、それぞれの全球の空間分布データ（```.nc```ファイル）を取得期間ぶんだけダウンロードするようにします。

```get_era5_data.py```の内容を以下のようにします。

```python
from configure import configure
from get_era5 import ERA5

# 設定値を取得する関数
dt1, dt2, lev, dir = configure()
print(f'{dt1}/{dt2}/{lev}')

# ERA5からデータを取得する関数
ERA5()

################################################################################
# 各データをダウンロードしてCSVファイルを生成
################################################################################
#-------------------------------------------------------------------------------
# ERA5 hourly data on pressure levels from 1940 to present
#-------------------------------------------------------------------------------
# 気温 (K)
ERA5.reanalysis_era5_pressure_levels('temperature', 't', lev, dt1, dt2, dir)

# 相対湿度 (%RH)
ERA5.reanalysis_era5_pressure_levels('relative_humidity', 'r', lev, dt1, dt2, dir)

#-------------------------------------------------------------------------------
# ERA5 hourly data on single levels from 1940 to present
#-------------------------------------------------------------------------------
# 気圧 (Pa)
ERA5.reanalysis_era5_single_levels('surface_pressure', 'sp', dt1, dt2, dir)

# 風速 (m/s)
ERA5.reanalysis_era5_single_levels('10m_u_component_of_wind', 'u10', dt1, dt2, dir)
ERA5.reanalysis_era5_single_levels('10m_v_component_of_wind', 'v10', dt1, dt2, dir)

# 降水量 (m/h)
ERA5.reanalysis_era5_single_levels('convective_precipitation', 'cp', dt1, dt2, dir)

# 全天日射量 (J/m2)
ERA5.reanalysis_era5_single_levels('surface_solar_radiation_downwards', 'ssrd', dt1, dt2, dir)
```

# Pythonスクリプトを自動実行する

データの取得期間を長くしたい場合、データ取得に時間がかかるため、バッチ処理で実行するようにします。

## シェル操作の場合（Ubuntu）

Ubuntu PCで```get_era5_data.py```を実行するシェルスクリプトを作成します。

```run.sh```の内容を以下のようにします。

```bash
#!/bin/bash

# ディレクトリ設定
script_dir="$(cd "$(dirname "$0")" && pwd)"
base_dir="$(dirname "$script_dir")"

# Pythonスクリプトを実行する
uv run "$base_dir/get-era5-data/get_era5_data.py"
```

# データを保存するストレージ

取得期間が長くなるとデータの容量も大きくなるため、上記の```<任意のディレクトリ>```を配置するストレージの容量には気をつける必要があります。

以下、ストレージの設定例を挙げます。

## pCloudを使用する場合

https://zenn.dev/ats030/articles/setup-pcloud-on-ubuntu

## OpenMediaVaultを使用する場合

https://zenn.dev/ats030/articles/mount-openmediavault-on-ubuntu