# Rasdaman-ImageImport-Guide
如何匯入影像到Rasdaman database中

1.編輯或新增ingredients_file，並存放在以下目錄中 
```
/opt/rasdaman/share/rasdaman/wcst_import/ingredients
```
以下介紹三種常用的匯入方式，如果想參考更多可用參數可閱讀下列文件  
```
/opt/rasdaman/share/rasdaman/wcst_import/ingredients/possible_ingredients.json
```
* 單張影像的ingredients_file:
```json
{
  "config": {
    "service_url": "http://localhost:8080/rasdaman/ows",
    "tmp_directory": "/tmp/",
    "crs_resolver": "http://localhost:8080/def/",
    "default_crs": "http://localhost:8080/def/crs/EPSG/0/3826", 這裡採用EPSG-3826，即97TM2座標系統
    "mock": false,
    "automated": false
  },
  "input": {
    "coverage_id": "你的coverage名稱",
    "paths": [
      "../影像名稱.tif"
    ]
  },
  "recipe": {
    "name": "map_mosaic",
    "options": {
      "tiling": "ALIGNED [*:*, *:*] ",
      "band_names":[            波段對應名稱，按自己影像的順序
         "Blue",
         "Green",
	 "Red"
     ]
    }
  }
}
```
* 時間不規律多張影像的ingredients_file:  
```json
{
  "config": {
    "service_url": "http://localhost:8080/rasdaman/ows",
    "tmp_directory": "/tmp/",
    "crs_resolver": "http://localhost:8080/def/",
    "default_crs": "http://localhost:8080/def/crs/EPSG/0/3826",
    "mock": false,
    "automated": false
  },
  "input": {
    "coverage_id": "你的coverage名稱",
    "paths": [
      "../*.tif"  input所有tif格式的影像
    ]
  },
  "recipe": {
    "name": "time_series_irregular",
    "options": {
      "time_parameter": {
        "filename": {
          "regex": "(.*)_(.*)_(.*)_(.*)\\.(.*)", 用regex批配影像名稱，這裡讀取的檔案名稱為BNPQD_2017-05-14_1_2.tif
          "group": "2" 檔名中的第二組為時間2017-05-14
        },
        "datetime_format": "YYYY-MM-DD" 時間格式
      },
      "time_crs": "http://localhost:8080/def/crs/OGC/0/AnsiDate",
      "tiling": "ALIGNED [*:*, *:*, *:*]",
      "band_names":[
         "Blue",
         "Green",
	 "Red",
	 "Red_Edge",
	 "NIR"
     ]
    }
  }
}
```
* 時間規律(固定間隔)多張影像的ingredients_file:  
```json
{
  "config": {
    "service_url": "http://localhost:8080/rasdaman/ows",
    "tmp_directory": "/tmp/",
    "crs_resolver": "http://localhost:8080/def/",
    "default_crs": "http://localhost:8080/def/crs/EPSG/0/3826",
    "mock": false,
    "automated": false
  },
  "input": {
    "coverage_id": "你的coverage名稱",
    "paths": [
      "../*.tif" 
    ]
  },
  "recipe": {
    "name": "time_series_regular",
    "options": {
      "time_start": "2012-12-02T20:12:02", 設定起始時間
      "time_format": "auto",
      "time_crs": "http://localhost:8080/def/crs/OGC/0/AnsiDate",
      "time_step": "2 days 10 minutes 3 seconds", 設定時間間隔
      "tiling": "ALIGNED [*:*, *:*, *:*]"
    }
  }
}
```
2.編輯完ingredients_file後在terminal裡使用wcst_import.sh，參數為ingredients_file  
```console
user@ubuntu:~$ wcst_impot.sh /opt/rasdaman/share/rasdaman/wcst_import/ingredients/你的ingredients_file
```
3.檢查是否匯入成功  
<http://localhost:8080/rasdaman/ows>  
有顯示coverageID及預覽就代表匯入成功囉!
