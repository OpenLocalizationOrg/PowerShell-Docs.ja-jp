#DSC パッケージのリソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

**パッケージ** リソースで Windows PowerShell 必要な状態 Configuration (DSC) がインストールまたはターゲット ノード上の Windows インストーラー、および setup.exe のパッケージなど、パッケージをアンインストールするメカニズムを提供します。

##構文

```
Package [string] #ResourceName
{
    Name = [string]
    Path = [string]
    ProductId = [string]
    [ Arguments = [string] ]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ LogPath = [string] ]
    [ DependsOn = [string[]] ]
    [ ReturnCode = [UInt32[]] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 特定の状態を保証するパッケージの名前を示します。|
| パス| パッケージが存在するパスを示します。|
| ProductId| パッケージを一意に識別する製品の ID を示します。|
| 引数| 提供されたとおり、パッケージに渡される引数の文字列の一覧を表示します。|
| Credential| リモート ソースには、パッケージへのアクセスを提供します。このプロパティは、パッケージのインストールには使用されません。パッケージは常に、ローカルのシステムにインストールします。|
| 確認します。| パッケージがインストールされていることを示します。「すべて」をパッケージがインストールされていないことを確認する (またはインストールされている場合は、パッケージをアンインストール) するには、このプロパティを設定します。パッケージがインストールされていることを確認する (既定値) を「表示」するように設定します。|
| LogPath| プロバイダーをインストールまたはアンインストール パッケージのログ ファイルを保存する場所の完全パスを示します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は、' DependsOn ="[リソースの種類] ResourceName"' です。|
| リターン コード| 想定される戻り値のコードを示します。実際にコードを返す場合に、予期される値の提供をここでは、構成には、エラーが返されますと一致しないはしません。|

##例

この例は、指定されたパスを指定した製品の ID を持つ .msi インストーラーを実行します。

```powershell
Package PackageExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Path  = "$Env:SystemDrive\TestFolder\TestProject.msi"
    Name = "TestPackage"
    ProductId = "ACDDCDAF-80C6-41E6-A1B9-8ABD8A05027E"
} 
```




