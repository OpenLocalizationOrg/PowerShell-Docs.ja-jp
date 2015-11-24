#DSC 環境のリソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

__環境__ リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、システム環境変数を管理するメカニズムを提供します。

##構文

``` mof
Environment [string] #ResourceName
{
    Name = [string]
    [ Ensure = [string] { Absent | Present }  ]
    [ Path = [bool] ]
    [ DependsOn = [string[]] ]
    [ Value = [string] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 特定の状態を保証する環境変数の名前を示します。|
| 確認します。| 変数が存在するかどうかを示します。このプロパティを設定 __存在__ が存在しない場合は、環境変数を作成する、またはその値は、を通じて提供されているものと一致することを確認する、 __値__ プロパティ、変数が既に存在する場合。設定 __存在しない__ して存在する場合は、変数を削除します。|
| パス| 構成されている環境変数を定義します。このプロパティを設定 __$true__ 変数の場合、 __パス__ 変数、それ以外に設定 __$false__です。既定値は __$false__です。変数のように構成されている場合は、 __パス__ を通じて提供される値を変数、 __値__ プロパティは、既存の値に追加されます。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| 値| 環境変数に代入する値。|

##例

次の例は、確実 __TestEnvironmentVariable__ が存在し、値がある __TestValue__です。 存在しない場合を作成します。

```powershell
Environment EnvironmentExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Name = "TestEnvironmentVariable"
    Value = "TestValue"
}
```




