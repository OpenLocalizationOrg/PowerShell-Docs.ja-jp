#DSC WindowsFeature リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

**WindowsFeature** リソースで Windows PowerShell 必要な状態 Configuration (DSC) が、役割と機能が追加または削除対象のノードでのことを確認するメカニズムを提供します。

##構文

```
WindowsFeature [string] #ResourceName
{
    Name = [string]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ IncludeAllSubFeature = [bool] ]
    [ LogPath = [string] ]
    [ DependsOn = [string[]] ]
    [ Source = [string] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 確認する役割または機能の名前を追加または削除することを示します。同じです。 これは、、 __名前__ プロパティから、 [Get-windowsfeature](https://technet.microsoft.com/en-us/library/jj205469.aspx) コマンドレットと役割または機能の表示名ではありません。|
| Credential| 追加または削除、役割または機能を使用する資格情報を示します。|
| 確認します。| 役割または機能が追加されたかどうかを示します。役割または機能があることを確認するには、追加され、設定、役割または機能が削除されるようにするには、"Present"には、このプロパティは「ない」にプロパティを設定します。|
| IncludeAllSubFeature| このプロパティを設定 __$true__ でを指定する必要なすべてのサブ機能の状態での状態を保証する、 __名前__ プロパティです。|
| LogPath| ログ ファイルが、操作を記録するリソース プロバイダーを先へのパスを示します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| ソース| 必要な場合は、インストールに使用するソース ファイルの場所を示します。|

##例

```powershell
WindowsFeature RoleExample
{
    Ensure = "Present" 
    # Alternatively, to ensure the role is uninstalled, set Ensure to "Absent"
    Name = "Web-Server" # Use the Name property from Get-WindowsFeature  
}
```





