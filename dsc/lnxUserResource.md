#DSC Linux nxUser リソース用

**NxUser** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux ノード上のローカル ユーザーを管理するメカニズムを提供します。

##構文

```
nxUser <string> #ResourceName
{
    UserName = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ FullName = <string> ]
    [ Description = <string> ]
    [ Password = <string> ]
    [ Disabled = <bool> ]
    [ PasswordChangeRequired = <bool> ]
    [ HomeDirectory = <string> ]
    [ Mode = <string> ]
    [ GroupID = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 特定の状態を保証するアカウント名を示します。|
|---|---|
| UserName| ファイルまたはディレクトリの状態を保証する場所を指定します。|
| 確認します。| アカウントが存在するかどうかを指定します。アカウントが存在することを確認するには、"Present"には、このプロパティを設定し、アカウントが存在しないことを確認する「ない」に設定します。|
| FullName| ユーザー アカウントを使用する完全な名前を文字列。|
| 説明| ユーザー アカウントの説明です。|
| Password| Linux コンピューター用の適切な形式でユーザーのパスワードのハッシュです。通常、これは、sha-256 ソルト処理、または sha-512 ハッシュです。Debian および Ubuntu Linux では、この値は、mkpasswd コマンドを使用して生成できます。その他の Linux ディストリビューションでは、Python の Crypt ライブラリの crypt メソッドはハッシュの生成に使用できます。|
| 無効になっています。| アカウントが有効になっているかどうかを示します。このプロパティを設定 **$true** このアカウントが無効にするとに設定することを確認する **$false** を有効になっていることを確認します。|
| PasswordChangeRequired| ユーザーがパスワードを変更するかどうかを示します。このプロパティを設定 **$true** 、ユーザーがパスワードを変更できないかどうか、およびに設定ことを確認に **$false** パスワードを変更するユーザーを許可します。既定値は **$false**です。このプロパティは、ユーザー アカウントは、以前に存在しなかったし、が作成される場合にのみ評価されます。|
| HomeDirectory| ユーザーのホーム ディレクトリ。|
| GroupID| ユーザーのプライマリ グループの ID です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば、実行するリソースの構成スクリプト ブロックの ID は、「リソース名」を最初は、その型は、「リソースの種類」場合は、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

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




