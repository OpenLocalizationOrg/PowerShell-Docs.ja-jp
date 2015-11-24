#DSC ユーザー リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:


__ユーザー__ リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、ターゲット ノード上のローカル ユーザー アカウントを管理するメカニズムを提供します。


##構文

```
User [string] #ResourceName
{
    UserName = [string]
    [ Description = [string] ]
    [ Disabled = [bool] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ FullName = [string] ]
    [ Password = [PSCredential] ]
    [ PasswordChangeNotAllowed = [bool] ]
    [ PasswordChangeRequired = [bool] ]
    [ PasswordNeverExpires = [bool] ]
    [ DependsOn = [string[]] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| UserName| 特定の状態を保証するアカウント名を示します。|
| 説明| ユーザー アカウントを使用する場合の説明を示します。|
| 無効になっています。| アカウントが有効になっているかどうかを示します。このプロパティを設定 __$true__ このアカウントが無効にするとに設定することを確認する __$false__ を有効になっていることを確認します。|
| 確認します。| アカウントが存在するかどうかを示します。アカウントが存在することを確認するには、"Present"には、このプロパティを設定し、アカウントが存在しないことを確認する「ない」に設定します。|
| FullName| ユーザー アカウントを使用する場合、完全名の文字列を表します。|
| Password| このアカウントを使用するパスワードを示します。|
| PasswordChangeNotAllowed| かどうか、ユーザーがパスワードを変更できることを示します。このプロパティを設定 __$true__ 、ユーザーがパスワードを変更できないかどうか、およびに設定ことを確認に __$false__ パスワードを変更するユーザーを許可します。既定値は __$false__です。|
| PasswordChangeRequired| かどうか、ユーザーは次回のサインイン時にパスワードを変更する必要がありますを示します。このプロパティを設定 __$true__ 場合は、ユーザーがパスワードを変更する必要があります。既定値は __$true__です。|
| PasswordNeverExpires| パスワードの有効期限が切れることを示します。パスワードのこのアカウントは有効期限が切れることはありません、このプロパティに設定する __$true__, に設定して __$false__ パスワードの有効期限が切れる場合。既定値は __$false__です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

```powershell
User UserExample
{
    Ensure = "Present"  # To ensure the user account does not exist, set Ensure to "Absent"
    UserName = "SomeName"
    Password = $passwordCred # This needs to be a credential object
    DependsOn = “[Group]GroupExample" # Configures GroupExample first
}
```




