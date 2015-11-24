#DSC Linux nxEnvironment リソース用

**NxEnvironment** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux ノード上のシステム環境変数を管理するメカニズムを提供します。

##構文

```
nxEnvironment <string> #ResourceName
{
    Name = <string>
    [ Value = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ Path = <bool> }
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 特定の状態を保証する環境変数の名前を示します。|
| 値| 環境変数に代入する値。|
| 確認します。| 変数が存在するかどうかを確認するかどうかを判断します。変数が存在することを確認するには、"Present"には、このプロパティを設定します。変数が存在しないことを確認するには「ない」に設定します。既定値は、"Present"です。|
| パス| 構成されている環境変数を定義します。このプロパティを設定 **$true** 変数の場合、 **パス** 変数、それ以外に設定 **$false**です。既定値は **$false**です。変数のように構成されている場合は、 **パス** を通じて提供される値を変数、 **値** プロパティは、既存の値に追加されます。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##追加情報

* 場合 **パス** が存在しないかに設定 **$false**, 、環境変数がで管理される `など/環境`です。 プログラムまたはスクリプトには、ソースに構成を必要があります、 `など/環境` 管理対象の環境変数にアクセスするファイルです。
* 場合 **パス** に設定されている **$true**, 、環境変数が、ファイルで管理されている `/etc/profile.d/DSCenvironment.sh`です。 存在しない場合、このファイルは作成されます。 場合 **を確認してください** 設定されている「存在しない」と **パス** に設定されている **$true**, 、既存の環境変数はからのみ削除されます `/etc/profile.d/DSCenvironment.sh` からその他のファイルではありません。

##例

次の例では、使用する方法、 **nxEnvironment** いることを確認するリソース **TestEnvironmentVariable** が存在し、「テスト値」の値を持つファイルです。 場合 **TestEnvironmentVariable** が存在しない場合は、作成されます。

```
Import-DSCResource -Module nx 


nxEnvironment EnvironmentExample
{
    Ensure = “Present”
    Name = “TestEnvironmentVariable”
    Value = “TestValue”
}
```






