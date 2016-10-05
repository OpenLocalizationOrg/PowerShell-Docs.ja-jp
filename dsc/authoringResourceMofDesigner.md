---
title: "リソース デザイナー ツールの使用"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: be2141330dda803a22fdce6d65a1e379adf14fed

---

# リソース デザイナー ツールの使用

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

リソース デザイナー ツールは、Windows PowerShell Desired State Configuration (DSC) リソースの作成を簡単にする、**xDscResourceDesigner** モジュールによって公開されている一連のコマンドレットです。 このリソースのコマンドレットは、MOF スキーマ、スクリプト モジュール、および新しいリソースのディレクトリ構造の作成に役立ちます。 DSC リソースの詳細については、「[カスタム Windows PowerShell Desired State Configuration のビルド](authoringResource.md)」を参照してください。
このトピックでは、Active Directory ユーザーを管理する DSC リソースを作成します。
[Install-Module](https://technet.microsoft.com/en-us/library/dn807162.aspx) コマンドレットを使用して **xDscResourceDesigner** モジュールをインストールします。

>**注**: **Install-Module** は、PowerShell 5.0 に含まれている **PowerShellGet** モジュールに含まれています。 「[PackageManagement PowerShell Modules Preview (PackageManagement PowerShell モジュールのプレビュー)](https://www.microsoft.com/en-us/download/details.aspx?id=49186)」で PowerShell 3.0 と 4.0 の **PowerShellGet** モジュールをダウンロードできます。

## リソース プロパティの作成
まず、リソースで公開するプロパティを決定する必要があります。 この例では、次のプロパティを持つ Active Directory ユーザーを定義します。
 
パラメーター名  説明
* **UserName**: ユーザーを一意に識別するキー プロパティです。
* **Ensure**: ユーザー アカウントが存在するかしないかを指定します。 このパラメーターに使用できる値は 2 つのみです。
* **DomainCredential**: ユーザーのドメイン パスワードです。
* **Password**: 必要に応じて構成でユーザー パスワードを変更できるようにするために必要なユーザーのパスワードです。

プロパティを作成するには、**New-xDscResourceProperty** コマンドレットを使用します。 次の PowerShell コマンドでは、上記で説明したプロパティを作成します。

```powershell
PS C:\> $UserName = New-xDscResourceProperty –Name UserName -Type String -Attribute Key
PS C:\> $Ensure = New-xDscResourceProperty –Name Ensure -Type String -Attribute Write –ValidateSet “Present”, “Absent”
PS C:\> $DomainCredential = New-xDscResourceProperty –Name DomainCredential-Type PSCredential -Attribute Write
PS C:\> $Password = New-xDscResourceProperty –Name Password -Type PSCredential -Attribute Write
```

## リソースの作成

リソース プロパティが作成されたので、**New-xDscResource** コマンドレットを呼び出してリソースを作成できます。 **New-xDscResource** コマンドレットは、パラメーターとしてプロパティのリストを受け取ります。 モジュールを作成するパス、新しいリソースの名前、そのリソースが含まれているモジュールの名前も受け取ります。 次の PowerShell コマンドでは、リソースを作成します。

```powershell
PS C:\> New-xDscResource –Name Demo_ADUser –Property $UserName, $Ensure, $DomainCredential, $Password –Path ‘C:\Program Files\WindowsPowerShell\Modules’ –ModuleName Demo_DSCModule
```

**New-xDscResource** コマンドレットは、MOF スキーマ、スケルトン リソース スクリプト、新しいリソースに必要なディレクトリ構造、および新しいリソースを公開するモジュールのマニフェストを作成します。

MOF スキーマ ファイルは、**C:\Program Files\WindowsPowerShell\Modules\Demo_DSCModule\DSCResources\Demo_ADUser\Demo_ADUser.schema.mof** にあり、その内容は次のとおりです。

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

リソース スクリプトは **C:\Program Files\WindowsPowerShell\Modules\Demo_DSCModule\DSCResources\Demo_ADUser\Demo_ADUser.psm1** にあります。 これには、自分自身を追加する必要があるリソースを実装する実際のロジックは含まれません。 スケルトン スクリプトの内容は次のとおりです。

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

## リソースの更新

リソースのパラメーター リストを追加または変更する必要がある場合は、**Update-xDscResource** コマンドレットを呼び出すことができます。 コマンドレットは、新しいパラメーター リストでリソースを更新します。 リソース スクリプトに既にロジックを追加している場合は、そのまま残ります。

たとえば、リソースにユーザーの最後のログイン時間を追加するとします。 リソースを完全に再作成するのではなく、**New-xDscResourceProperty** を呼び出して新しいプロパティを作成してから **Update-xDscResource** を呼び出し、プロパティ リストに新しいプロパティを追加できます。

```powershell
PS C:\> $lastLogon = New-xDscResourceProperty –Name LastLogon –Type Hashtable –Attribute Write –Description “For mapping users to their last log on time”
PS C:\> Update-xDscResource –Name ‘Demo_ADUser’ –Property $UserName, $Ensure, $DomainCredential, $Password, $lastLogon -Force
```

## リソース スキーマのテスト

リソース デザイナー ツールは、手動で記述した MOF スキーマの有効性をテストするために使用できる 1 つ以上のコマンドレットを公開します。 パラメーターとして MOF リソース スキーマのパスを渡して、**Test-xDscSchema** コマンドレットを呼び出します。 コマンドレットは、スキーマにエラーを出力します。

### 参照

#### 概念
[Build Custom Windows PowerShell Desired State Configuration Resources (カスタム Windows PowerShell Desired State Configuration のビルド)](authoringResource.md)

#### その他のリソース
[xDscResourceDesigner モジュール](https://powershellgallery.com/packages/xDscResourceDesigner)




<!--HONumber=Oct16_HO1-->


