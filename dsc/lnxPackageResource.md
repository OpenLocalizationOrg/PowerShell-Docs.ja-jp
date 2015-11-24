#DSC Linux nxPackage リソース用

**NxPackage** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux のノード上でパッケージを管理するメカニズムを提供します。

##構文

```
nxPackage <string> #ResourceName
{
    Name = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ PackageManager = <string> { Yum | Apt | Zypper } ]
    [ PackageGroup = <bool>]
    [ Arguments = <string> ]
    [ ReturnCode = <uint32> ]
    [ LogPath = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 特定の状態を保証するパッケージの名前。|
| 確認します。| パッケージが存在するかどうかを確認するかどうかを判断します。パッケージが存在することを確認するには、"Present"には、このプロパティを設定します。パッケージが存在しないことを確認する「ない」に設定します。既定値は、"Present"です。|
| PackageManager| サポートされている値は"yum"、「適切」、および"zypper"です。パッケージをインストールするときに使用するパッケージ マネージャーを指定します。場合 **FilePath** が指定されている、指定したパスは、パッケージをインストールするために使用されます。それ以外の場合、パッケージ マネージャーが使用して事前に構成されているリポジトリからパッケージをインストールします。どちらの場合 **PackageManager** も **FilePath** 入力については、既定のパッケージ マネージャー、システムが使用されます。|
| ファイル パス| パッケージが存在するファイルのパス|
| PackageGroup| 場合 **$true**, 、 **名前** で使用するためのパッケージのグループの名前を指定する必要が、 **PackageManager**です。**PacakgeGroup** を提供する場合は無効にする **FilePath**です。|
| 引数| 提供されたとおり、パッケージに渡される引数の文字列。|
| リターン コード| 予期されるには、コードが返されます。実際にコードを返す場合に、予期される値の提供をここでは、構成には、エラーが返されますと一致しないはしません。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

次の例では、"Yum"パッケージ マネージャーを使用して、Linux コンピューターで"httpd"をという名前のパッケージがインストールされていることを確認します。

```
Import-DSCResource -Module nx 

Node $node {
nxPackage httpd
{
    Name = "httpd"
    Ensure = "Present"
    PackageManager = "Yum"
}
}
```




