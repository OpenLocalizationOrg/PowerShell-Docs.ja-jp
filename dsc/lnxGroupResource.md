#DSC Linux nxGroup リソース用

**NxGroup** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux のノード上のローカル グループを管理するメカニズムを提供します。

##構文

```powershell
nxGroup <string> #ResourceName
{
    GroupName = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ Members = <string[]> ]
    [ MebersToInclude = <string[]>]
    [ MembersToExclude = <string[]> ]
    [ DependsOn = <string[]> ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| グループ名| 特定の状態を確認するグループの名前を指定します。|
| 確認します。| グループが存在するかどうかを確認するかどうかを判断します。グループが存在することを確認するには、"Present"には、このプロパティを設定します。グループが存在しないことを確認する「ない」に設定します。既定値は、"Present"です。|
| メンバー| グループを形成するメンバーを指定します。|
| MembersToInclude| 確認するユーザーには、グループのメンバーを指定します。|
| MembersToExclude| データ型は、グループのメンバーを確認するユーザーではないことを指定します。|
| PreferredGroupID| 可能であればグループ id を指定された値に設定します。グループ id は、現在使用中に、[次へ] の使用可能なグループの id が使用されます。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

次の例では、"monuser"ユーザーが存在し、"DBusers"のグループのメンバーであることをようにします。

```
Import-DSCResource -Module nx 

Node $node {

nxUser UserExample{
   UserName = "monuser"
   Description = "Monitoring user"
   Password  =    '$6$fZAne/Qc$MZejMrOxDK0ogv9SLiBP5J5qZFBvXLnDu8HY1Oy7ycX.Y3C7mGPUfeQy3A82ev3zIabhDQnj2ayeuGn02CqE/0'
   Ensure = "Present"
   HomeDirectory = "/home/monuser"
}

nxGroup GroupExample{
   GroupName = "DBusers"
   Ensure = "Present"
   MembersToInclude = "monuser"
   DependsOn = "[nxUser]UserExample"            
}
}
```




