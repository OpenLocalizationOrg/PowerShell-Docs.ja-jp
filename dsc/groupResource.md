#DSC グループ リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

グループのリソースで Windows PowerShell 必要な状態 Configuration (DSC) では、ターゲット ノード上のローカル グループを管理するためのメカニズムを提供します。

##構文

```
Group [string] #ResourceName
{
    GroupName = [string]
    [ Credential = [PSCredential] ]
    [ Description = [string[]] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ Members = [string[]] ]
    [ MembersToExclude = [string[]] ]
    [ MembersToInclude = [string[]] ]
    [ DependsOn = [string[]] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| グループ名| 特定の状態を確認するグループの名前を示します。|
| Credential| リモート リソースにアクセスするために必要な資格情報を示します。**注**: このアカウントは、グループにすべての非ローカル アカウントを追加する適切な Active Directory のアクセス許可が必要があります。 それ以外の場合、エラーが発生します。
| 説明| グループの説明を示します。|
| 確認します。| グループが存在するかどうかを示します。「すべて」をグループが存在しないことを確認するには、このプロパティを設定します。(既定値) の「表示」するには、次のように設定とは、グループが存在することを確認します。|
| メンバー| これらのメンバー グループを形成することを確認することを示します。|
| MembersToExclude| データ型は、このグループのメンバーを確認しユーザーではないことを示します。|
| MembersToInclude| 確認するユーザーには、グループのメンバーを示します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は、' DependsOn ="[リソースの種類] ResourceName"' です。|

##例

次の例では、その後 TestGroup をという名前のグループが存在しないことを確認する方法を示します。

```powershell
Group GroupExample
{
    # This will remove TestGroup, if present
    # To create a new group, set Ensure to "Present“
    Ensure = "Absent"
    GroupName = "TestGroup"
}
```




