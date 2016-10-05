# Find-Script

指定した条件に一致するオンライン ギャラリーからする PowerShell スクリプト ファイルを検索します。

## 説明

検索スクリプトでは、指定した条件に一致する登録されているリポジトリからのスクリプト ファイルを検出します。
検出された各スクリプトは、検索スクリプトは、スクリプトをインストールするため、インストール スクリプトを必要に応じてパイプすることもできる PSRepositoryItemInfo オブジェクトを返します。
Find-Script コマンドレットでは、名前、タグ、フィルター、コマンド名、バージョン範囲、正確なバージョン、すべてのバージョンなどのさまざまな検索条件を使用して、特定またはすべての登録済みリポジトリから、スクリプト ファイルをその依存関係を含めて検出できます。

- 検索スクリプトをスクリプトに基づくフィルター処理は、- コマンドを使用してコンテンツし、のパラメーターが含まれています。
- 検索スクリプトがバージョン パラメーターを使用してフィルター処理できます。 MinimumVersion、MaximumVersion、RequiredVersion、AllVersions です。
  - これらのパラメーターは MinmimumVersion と MaximumVersion を除く、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのスクリプト名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、検索スクリプトは、最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、スクリプトの最新のバージョンよりも大きいは、スクリプトの最新バージョンを返します。 
  - RequiredVersion パラメーターが指定されている場合、検索スクリプトには、指定されたバージョンを完全に一致するスクリプトのバージョンのみを返します。
- スクリプトのメタデータの検索スクリプトをフィルターできますタグ パラメーターを使用します。
- リポジトリに固有の検索の言語の検索スクリプトを絞り込むフィルター パラメーターを使用します。
- 検索スクリプトが登録されているリポジトリのほとんどまたはすべてからのスクリプトでフィルター処理できます。

**注:** 登録されている PSRepository が有効な ScriptSourceLocation を持つ必要があります。 セット PSRepository を使用して、ScriptSourceLocation 値を設定することができます。

## コマンドレット構文

```powershell
Get-Command -Name Find-Script -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[検索スクリプト](http://go.microsoft.com/fwlink/?LinkId=619785)

## コマンド例

```powershell
# Find a script from the registered repository with ScriptSourceLocation
Find-Script Connect-AzureVM

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.0        Connect-AzureVM                     PSGallery            This runbook sets up a connection to an Azure vi...

# Find multiple scripts
Find-Script -Name Connect-AzureVM, Show-Tree, Connect-O365

# Find scripts with wildcards in -Name
Find-Script -Name *Azure*

# Find all versions of a script
Find-Script -Name Connect-O365 -AllVersions

# Find a script with -MinimumVersion. 
# With MinimumVersion we can find a script whose version is greate than or equal to the specified MinimumVersion value.
Find-Script Connect-O365 -MinimumVersion 1.4

# Find a script with MaximumVersion
Find-Script -Name Connect-O365 -MaximumVersion 1.6.2

# Find a script with both MinimumVersion and MaximumVersion range.
Find-Script -Name Connect-O365 -MinimumVersion 1.1 -MaximumVersion 1.6.2

# Find a script with exact version
Find-Script -Name Connect-O365 -RequiredVersion 1.5.7

# Find a script from the specified repository
Find-Script -Name Fabrikam-ServerScript -Repository MyLocalRepo

# Find available scripts from all registered repositories
Find-Script

# Find available scripts from few registered repositories
Find-Script -Repository PSGallery, PrivatePSGallery

# Find a script along with its dependent modules and scripts
Find-Script -Name Connect-AzureVM -IncludeDependencies

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.0        Connect-AzureVM                     PSGallery            This runbook sets up a connection to an Azure vi...
1.4.0      Azure                               PSGallery            Microsoft Azure PowerShell - Service Management
1.1.2      Azure.Storage                       PSGallery            Microsoft Azure PowerShell - Storage service cmd...
1.0.8      AzureRM.profile                     PSGallery            Microsoft Azure PowerShell - Profile credential ...

# Find all scripts with workflows
Find-Script -Includes Workflow

# Find all scripts with functions
Find-Script -Includes Function

# Find scripts with specific commands
Find-Script -Command Log-Message
Find-Script -Command Log-Message, Show-Tree -Includes Function
Find-Script -Command Connect-AzureVM -Includes Workflow

# Find scripts with -Filter based search. -Filter searches in description and names
Find-Script -Filter Windows
Find-Script -Filter Azure

# Find all scripts with tags O365 or Nano
Find-Script -Tag O365, Nano

# Properties of Find-Script returned object
Find-Script Show-Tree | Format-List * -Force
Name                       : Show-Tree
Version                    : 1.0.0
Type                       : Script
Description                : Script to show the layout of PowerShell namespaces (Trees) using ASCII
Author                     : Jeffrey Snover
CompanyName                : jsnover
Copyright                  : (C) Microsoft Corporation. All rights reserved.
PublishedDate              : 2/15/2016 10:15:35 PM
InstalledDate              :
UpdatedDate                :
LicenseUri                 :
ProjectUri                 :
IconUri                    :
Tags                       : {Nano, PSScript}
Includes                   : {Function, RoleCapability, Command, DscResource...}
PowerShellGetFormatVersion :
ReleaseNotes               :
Dependencies               : {}
RepositorySourceLocation   : https://www.powershellgallery.com/api/v2/
Repository                 : PSGallery
PackageManagementProvider  : NuGet
AdditionalMetadata         : {versionDownloadCount, ItemType, copyright, PackageManagementProvider...}


# Includes property on PSRepositoryItemInfo object
$t = Find-Script -Includes Workflow -Repository INT -Name Fabrikam-ClientScript
$t.Includes

Name                           Value
----                           -----
Function                       {Test-FunctionFromScript_Fabrikam-ClientScript}
RoleCapability                 {}
Command                        {Test-FunctionFromScript_Fabrikam-ClientScript, Test-WorkflowFromScript_Fabrikam-Clie...
DscResource                    {}
Workflow                       {Test-WorkflowFromScript_Fabrikam-ClientScript}
Cmdlet                         {}


```


<!--HONumber=Oct16_HO1-->


