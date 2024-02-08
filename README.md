# chizu-xml1
for downloading chizu xml

# Powershell script (list the file names in list.txt)


```
$jsonData = (curl.exe 'https://www.geospatial.jp/ckan/api/3/action/resource_search?query=name:-2023.zip&limit=2005' | ConvertFrom-JSON)

cd data
for ($i=0; $i -lt $jsonData.result.results.name.length ; $i++ ){
  $check = ""
  $check = Select-String $jsonData.result.results.name[$i] "list.txt"
  if($check.length -eq 1){
    curl.exe -LO $jsonData.result.results.URL[$i]
  }
}
```


# Powershell script （xmlファイルの投影法をリストする。）

（前提）
作業フォルダに対象とする「市区町村-法務局」別のZIPファイルを格納する。
WindowsPowershellで実行する。

```
# 市区町村の地図XMLパッケージ（ZIP）をフォルダに格納。
# その場所でPowerShellを実行する。
#========================================

# 作業フォルダの準備
#rm unpack
#rm list
mkdir unpack
mkdir list

# 出力ファイル名を定義
$resultTXT = "projection-list.txt"
$fileSize = "xml-file-size.txt"

# 出力ファイルを初期化
if(Test-Path $resultTXT){
  rm $resultTXT
}
if(Test-Path $fileSize){
  rm $fileSize
}


# XMLの市区町村パッケージ（ZIP）リストの取得
$zipList = Get-Childitem -Path . -filter *.zip

##################
# 各市区町村ごとのループ開始
foreach($zippedPack in $zipList){

  # 市区町村のパッケージfileをunpackフォルダに解凍
  Expand-Archive $zippedPack -DestinationPath unpack
  # unpackフォルダ中のZIPファイルをリスト
  $testList = Get-Childitem -Path unpack -filter *.zip
  # unpackフォルダ内のzipファイルを解凍
  foreach($item in $testList){
    Expand-Archive unpack/$item -DestinationPath unpack 
    Write-host($item)
  }

  # 解凍したlistを移動
  Move-Item unpack/*.csv -Destination list

  # unpackフォルダ内のzipファイルを削除
  rm unpack/*.zip

  # unpack フォルダ内のxmlファイルの座標系を検索。ファイル一覧も記録。
 
  if ($zahyo) {
    remove-variable zahyo
  }
  $zahyo = Select-String "座標系" unpack/*.xml
  Write-Output $zahyo | Add-Content $resultTXT
  #Get-Item unpack/* | Add-Content $fileSize
  Get-Item unpack/* >> $fileSize

  # unpackフォルダをクリーン
  rm unpack/*
}
```
