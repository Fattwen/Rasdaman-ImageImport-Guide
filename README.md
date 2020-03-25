# Rasdaman-ImageImport-Guide
如何匯入影像到Rasdaman database中

1.編輯或新增ingredients_file，並存放在  
```
/opt/rasdaman/share/rasdaman/wcst_import/ingredients
```
參數可參考  
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
          "regex": "(.*)_(.*)_(.*)_(.*)\\.(.*)",  regex批配影像名稱
          "group": "2"
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
2.在terminal裡使用wcst_import.sh
```console
user@ubuntu:~$ wcst_impot.sh /opt/rasdaman/share/rasdaman/wcst_import/ingredients/你的ingredients_file
```
