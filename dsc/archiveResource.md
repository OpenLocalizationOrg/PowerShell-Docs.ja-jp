#DSC アーカイブのリソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

アーカイブのリソースで Windows PowerShell 必要な状態 Configuration (DSC) では、特定のパスでのアーカイブ (.zip) ファイルをアンパックするためのメカニズムを提供します。

##構文

```MOF
Archive [string] #ResourceName
{
    Destination = [string]
    Path = [string]
    [ Checksum = [string] { CreatedDate | ModifiedDate | SHA-1 | SHA-256 | SHA-512 }  ]
    [ DependsOn = [string[]] ]
    [ Ensure = [string] { Absent | Present }  ]
   [Force = [bool] ]
    [ Validate = [bool] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Destination| アーカイブの内容を抽出することを確認する場所を指定します。|
| パス| アーカイブ ファイルのソース パスを指定します。|
| __チェックサム__| 2 つのファイルが同じであるかどうかを決定するときに使用する型を定義します。場合 __チェックサム__ が指定されていない、比較のため、ファイルまたはディレクトリ名のみを使用します。有効な値には、:、sha-1、sha-256、SHA 512、createdDate、modifiedDate なし (既定)。指定した場合 __チェックサム__ せず __検証__, 、構成が失敗します。|
| 確認します。| 存在する場合、アーカイブの内容を確認するかどうか、 __送信先__です。このプロパティを設定 __存在__ 内容がの存在を確認します。設定 __存在しない__ に存在しないことを確認します。既定値は __存在__です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。ResourceName とその型を実行するリソースの構成スクリプト ブロックの ID が最初がある場合は、たとえば、 __ResourceType__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| 検証| アーカイブに署名が一致するかどうかを判断するのにには、チェックサムのプロパティを使用します。チェックサム検証なしを指定する場合は、構成は失敗します。せず、チェックサムの検証を指定すると、既定では、sha-256 チェックサムが使用されます。|
| Force| 特定のファイル操作 (ファイルを上書きするが空でないディレクトリを削除するなど) すると、エラーが発生します。Force プロパティを使用すると、このようなエラーがよりも優先します。既定値は False です。|

##例

次の例では、アーカイブのリソースを使用して、Test.zip と呼ばれるアーカイブ ファイルの内容が存在し、特定の転送先に抽出されたことを確認する方法を示します。

```
Archive ArchiveExample {
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Path = "C:\Users\Public\Documents\Test.zip"
    Destination = "C:\Users\Public\Documents\ExtractionPath"
} 
```




