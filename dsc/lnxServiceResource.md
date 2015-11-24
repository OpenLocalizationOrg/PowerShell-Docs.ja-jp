#DSC Linux nxService リソース用

**NxService** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux ノード上のサービスを管理するメカニズムを提供します。

##構文

```
nxService <string> #ResourceName
{
    Name = <string>
    [ Controller = <string> { init | upstart | system }  ]
    [ Enambled = <bool> ]
    [ State = <string> { Running | Stopped } ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| Name| 構成するサービス/デーモンの名前。|
| コント ローラー| サービスを構成するときに使用するサービスのコント ローラーの型。|
| Enabled| 起動時にサービスを開始するかどうかを示します。|
| 状態| サービスが実行されているかどうかを示します。サービスが実行されていないことを確認するには、"Stopped"には、このプロパティを設定します。サービスが実行されていないことを確認するには「実行中」に設定します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|


##追加情報

**NxService** リソースは作成できません、サービスの定義や、サービスのスクリプトが存在しない場合。 PowerShell の [状態の必要な構成を使用する **nxFile** 存在または、サービス定義ファイルまたはスクリプトの内容を管理するリソースです。

##例

次の例では、サービスの構成、"httpd"(Apache HTTP Server) 用に登録されている、 **SystemD** サービス コント ローラーです。

```
Import-DSCResource -Module nx 

Node $node {
#Apache Service
nxService ApacheService 
{
Name = "httpd"
State = "running"
Enabled = $true
Controller = "systemd"
}
}
```




