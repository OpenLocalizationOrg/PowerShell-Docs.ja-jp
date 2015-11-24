#DSC サービス リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:


**サービス** リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、ターゲット ノード上のサービスを管理するメカニズムを提供します。

##構文

```
Service [string] #ResourceName
{
    Name = [string]
    [ BuiltInAccount = [string] { LocalService | LocalSystem | NetworkService }  ]
    [ Credential = [PSCredential] ]
    [ DependsOn = [string[]] ]
    [ StartupType = [string] { Automatic | Disabled | Manual }  ]
    [ State = [string] { Running | Stopped }  ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| サービス名を示します。場合によっては異なる表示名からに注意してください。サービスと、現在の状態とは、Get-service コマンドレットの一覧を取得することができます。|
| BuiltInAccount| サインインに使用するアカウント、サービスを示します。このプロパティで許可されている値は、: **LocalService**, 、**LocalSystem**, 、および **NetworkService**です。|
| Credential| サービスを実行するアカウントの資格情報を示します。このプロパティと __BuiltinAccount__ プロパティを一緒に使用することはできません。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| StartupType| サービスのスタートアップの種類を示します。このプロパティで許可されている値は、: **自動**, 、**無効になっている**, 、および **手動**|
| 状態| サービスのことを確認する状態を示します。|

##例

```powershell
Service ServiceExample
{
    Name = "TermService"
    StartupType = "Manual"
    State = "Running"
} 
```




