#DSC Linux nxArchive リソース用

**NxArchive** リソース PowerShell 必要な状態 Configuration (DSC) では Linux ノードで、特定のパスでアーカイブ (.tar、.zip) ファイルをアンパックするメカニズムを提供します。

##構文

```
nxArchive <string> #ResourceName
{
    SourcePath = <string>
    DestinationPath = <string>
    [ Checksum = <string> { ctime | mtime | md5 }  ]
    [ Force = <bool> ]
    [ DependsOn = <string[]> ]
    [ Ensure = <string> { Absent | Present }  ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| SourcePath| アーカイブ ファイルのソース パスを指定します。.Tar があります、.zip または。. tar. gz ファイルです。|
| DestinationPath| アーカイブの内容を抽出することを確認する場所を指定します。|
| チェックサム| ソースのアーカイブが更新されたかどうかを決定するときに使用する型を定義します。値は、:"mtime"、"ctime"または"md5"です。既定値は、"md5"です。|
| Force| 特定のファイル操作 (ファイルを上書きするが空でないディレクトリを削除するなど) すると、エラーが発生します。使用して、 **Force** プロパティには、このようなエラーがよりも優先されます。既定値は **$false**です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| 確認します。| 存在する場合、アーカイブの内容を確認するかどうか、 **送信先**です。このプロパティをコンテンツが存在することを確認するには、"Present"を設定します。存在しないことを確認するには「ない」に設定します。既定値は、"Present"です。|

##例

次の例では、使用する方法、 **nxArchive** アーカイブ ファイルの内容が呼び出されることを確認するリソース `website.tar` 存在し、特定の転送先に抽出されます。

```
Import-DSCResource -Module nx 

nxFile SyncArchiveFromWeb
{
   Ensure = "Present"
   SourcePath = “http://release.contoso.com/releases/website.tar”
   DestinationPath = "/usr/release/staging/website.tar"
   Type = "File"
   Checksum = “mtime”
}

nxArchive SyncWebDir
{
   SourcePath = “/usr/release/staging/website.tar”
   DestinationPath = “/usr/local/apache2/htdocs/”
   Force = $false
   DependsOn = "[nxFile]SyncArchiveFromWeb"
} 
```




