#リソース デザイナー ツールを使用します。

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

リソース デザイナー ツールには、一連のコマンドレットによって公開されている、 **xDscResourceDesigner** 作成の Windows PowerShell 必要な状態 Configuration (DSC) のリソースを簡単にするモジュールです。 このリソースのコマンドレットは、MOF スキーマ、スクリプトのモジュール、および、新しいリソースのディレクトリ構造の作成に役立ちます。 DSC リソースに関する詳細については、次を参照してください。 [ビルド カスタム Windows PowerShell 必要な状態の構成に関するリソース](authoringResource.md)です。
このトピックでは、Active Directory ユーザーを管理する DSC リソースを作成します。
使用して、 [インストール モジュール](https://technet.microsoft.com/en-us/library/dn807162.aspx) コマンドレットをインストールする、 **xDscResourceDesigner** モジュール。

> **注**: **インストール モジュール** に含まれる、 **PowerShellGet** モジュールでは、PowerShell 5.0 に含まれています。 ダウンロードすることができます、 **PowerShellGet** PowerShell 3.0 と 4.0 のモジュール [また PowerShell モジュールのプレビュー](https://www.microsoft.com/en-us/download/details.aspx?id=49186)です。

##リソースのプロパティを作成します。

最初の処理を行う必要がありますが、リソースを公開するプロパティを決定します。 この例では、Active Directory ユーザーと、次のプロパティを定義します。

パラメーター名の説明
* **UserName**: ユーザーを一意に識別するプロパティのキーします。
* **を確認してください**。 存在しないか、ユーザー アカウントに存在する必要がありますかを指定します。 このパラメーターには、2 つだけ指定できる値があります。
* **DomainCredential**: ユーザーのドメインのパスワード。
* **パスワード**: 必要に応じて、ユーザーのパスワードを変更する構成を許可するユーザーの必要なパスワード。

使用して、プロパティを作成する、 **新規 xDscResourceProperty** コマンドレットです。 次の PowerShell コマンドでは、上記で説明したプロパティを作成します。

```powershell
PS C:\> $UserName = New-xDscResourceProperty –Name UserName -Type String -Attribute Key
PS C:\> $Ensure = New-xDscResourceProperty –Name Ensure -Type String -Attribute Write –ValidateSet “Present”, “Absent”
PS C:\> $DomainCredential = New-xDscResourceProperty –Name DomainCredential-Type PSCredential -Attribute Write
PS C:\> $Password = New-xDscResourceProperty –Name Password -Type PSCredential -Attribute Write
```

##リソースを作成します。

呼び出して、リソースのプロパティを作成すると、 **新規 xDscResource** コマンドレットをリソースを作成します。  **新規 xDscResource** コマンドレットは、パラメーターとしてプロパティの一覧を取得します。 また、モジュールを作成するか、パスや、新しいリソースの名前が含まれているモジュールの名前も受け取ります。 次の PowerShell コマンドでは、リソースを作成します。

```powershell
PS C:\> New-xDscResource –Name Demo_ADUser –Property $UserName, $Ensure, $DomainCredential, $Password –Path ‘C:\Program Files\WindowsPowerShell\Modules’ –ModuleName Demo_DSCModule
```

**新規 xDscResource** コマンドレットは、MOF スキーマ、スケルトンのリソース スクリプト、新しいリソースを必要なディレクトリ構造と、新しいリソースを公開するモジュールのマニフェストを作成します。

MOF のスキーマ ファイルにある **C:\Program Files\WindowsPowerShell\Modules\Demo_DSCModule\DSCResources\Demo_ADUser\Demo_ADUser.schema.mof**, 、し、その内容は次のとおりです。

```
[ClassVersion("1.0.0.0"), FriendlyName("Demo_ADUser")]
class Demo_ADUser : OMI_BaseResource
{
[Key] string UserName;
[Write, ValueMap{"Present","Absent"}, Values{"Present","Absent"}] string Ensure;
[Write, EmbeddedInstance("MSFT_Credential")] String DomainCredential;
[Write, EmbeddedInstance("MSFT_Credential")] String Password;
};
```

リソースのスクリプトが、 **C:\Program Files\WindowsPowerShell\Modules\Demo_DSCModule\DSCResources\Demo_ADUser\Demo_ADUser.psm1**です。 実際のロジックを自分で追加する必要がありますリソースを実装するのには含まれません。 スケルトン スクリプトの内容は次のとおりです。

```powershell
function Get-TargetResource
{
[CmdletBinding()]
[OutputType([System.Collections.Hashtable])]
param
(
[parameter(Mandatory = $true)]
[System.String]
$UserName
)

#Write-Verbose "Use this cmdlet to deliver information about command processing."

#Write-Debug "Use this cmdlet to write debug information while troubleshooting."


<#
$returnValue = @{
UserName = [System.String]
Ensure = [System.String]
DomainAdminCredential = [System.Management.Automation.PSCredential]
Password = [System.Management.Automation.PSCredential]
}

$returnValue
#>
}


function Set-TargetResource
{
[CmdletBinding()]
param
(
[parameter(Mandatory = $true)]
[System.String]
$UserName,

[ValidateSet("Present","Absent")]
[System.String]
$Ensure,

[System.Management.Automation.PSCredential]
$DomainAdminCredential,

[System.Management.Automation.PSCredential]
$Password
)

#Write-Verbose "Use this cmdlet to deliver information about command processing."

#Write-Debug "Use this cmdlet to write debug information while troubleshooting."

#Include this line if the resource requires a system reboot.
#$global:DSCMachineStatus = 1


}


function Test-TargetResource
{
[CmdletBinding()]
[OutputType([System.Boolean])]
param
(
[parameter(Mandatory = $true)]
[System.String]
$UserName,

[ValidateSet("Present","Absent")]
[System.String]
$Ensure,

[System.Management.Automation.PSCredential]
$DomainAdminCredential,

[System.Management.Automation.PSCredential]
$Password
)

#Write-Verbose "Use this cmdlet to deliver information about command processing."

#Write-Debug "Use this cmdlet to write debug information while troubleshooting."


<#
$result = [System.Boolean]

$result
#>
}


Export-ModuleMember -Function *-TargetResource
```

##リソースの更新

追加またはリソースのパラメーター リストを変更する必要がある場合は、呼び出す、 **更新 xDscResource** コマンドレットです。 コマンドレットでは、新しいパラメーター リストで、リソースを更新します。 リソース スクリプトで既にロジックを追加した場合に変更されません。

たとえば、次のようなユーザー、リソースでの時刻で、最後のログを追加するとします。 リソースを完全に再作成する代わりに呼び出すことができます、 **新規 xDscResourceProperty** を新しいプロパティを作成し、呼び出す **更新 xDscResource** し、プロパティの一覧に、新しいプロパティを追加します。

```powershell
PS C:\> $lastLogon = New-xDscResourceProperty –Name LastLogon –Type Hashtable –Attribute Write –Description “For mapping users to their last log on time”
PS C:\> Update-xDscResource –Name ‘Demo_ADUser’ –Property $UserName, $Ensure, $DomainCredential, $Password, $lastLogon -Force
```

##リソースのスキーマをテストします。

リソース デザイナー ツールを手動で記述した MOF スキーマの有効性をテストするために使用する 1 つ以上のコマンドレットを公開します。 呼び出す、 **テスト xDscSchema** コマンドレットをパラメーターとして MOF リソースのスキーマのパスを渡します。 このコマンドレットでは、スキーマ内のすべてのエラーを出力します。

###関連項目

####概念

[カスタムの Windows PowerShell が必要な状態の構成のリソースを作成します。](authoringResource.md)

####その他のリソース

[xDscResourceDesigner モジュール](https://powershellgallery.com/packages/xDscResourceDesigner)




