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
