# Find-Module
指定した条件に一致するオンライン ギャラリーからモジュールを検索します。

## 説明
検索モジュールは、指定した条件に一致する登録されているリポジトリからモジュールを検出します。
検出された各モジュールは、検索モジュールは、モジュールをインストールするため、モジュールのインストールに必要に応じてパイプすることもできる PSRepositoryItemInfo オブジェクトを返します。

- 検索モジュールがモジュールに基づくフィルター処理は、コマンド、- DscResource、RoleCapability と内容し、-パラメーターが含まれています。
- バージョン パラメーターを使用してモジュールの検索を絞り込む: MinimumVersion、MaximumVersion、RequiredVersion、AllVersions です。
  - これらのパラメーターは MinmimumVersion と MaximumVersion を除く、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのモジュール名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、検索モジュールは、最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、モジュールの最新のバージョンよりも大きいは、モジュールの最新バージョンを返します。 
  - RequiredVersion パラメーターが指定されている場合、検索モジュールには、指定されたバージョンを完全に一致するモジュールのバージョンのみを返します。
- Find-Module では、-Tag パラメーターを使用してモジュールのメタデータをフィルター処理できます。
- リポジトリに固有の検索の言語にモジュールの検索を絞り込むフィルター パラメーターを使用します。
- 検索モジュールは、登録されているリポジトリのほとんどまたはすべてのモジュールにフィルター処理できます。

## コマンドレット構文
```powershell
Get-Command -Name Find-Module -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[モジュールの検索](http://go.microsoft.com/fwlink/?LinkID=398574)

## コマンド例
```powershell
# Find a specific module
Find-Module Azure

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.3.2      Azure                               PSGallery            Microsoft Azure PowerShell - Service Management

# Find multiple modules
Find-Module Azure,AzureRM

# Find modules with wildcards in -Name
Find-Module -Name AzureRM*

# Find all versions of a module
Find-Module -Name PSReadline -AllVersions

# Find a module with -MinimumVersion. 
# With MinimumVersion we can find a module whose version is greate than or equal to the specified MinimumVersion value.
Find-Module -Name PSReadline -MinimumVersion 1.0.0.12

# Find a module with MaximumVersion
Find-Module -Name PSReadline -MaximumVersion 1.0.0.13

# Find a module with both MinimumVersion and MaximumVersion range.
Find-Module -Name PSReadline -MinimumVersion 1.0.0.12 -MaximumVersion 1.0.0.13

# Find a module with exact version
Find-Module -Name AzureRM -RequiredVersion 1.3.2

# Find a module from the specified repository
Find-Module -Name Contoso -Repository MyLocalRepo

# Find available modules from all registered repositories
Find-Module

# Find available modules from few registered repositories
Find-Module -Repository PSGallery,PrivatePSGallery

# Find a module along with its dependencies
Find-Module -Name AzureRM -IncludeDependencies

# Find all modules with Dsc resources
Find-Module -Includes DscResource

# Find modules with a specific DscResource
Find-Module -DscResource xFirewall

# Find all modules with cmdlets
Find-Module -Includes Cmdlet

# Find all modules with functions
Find-Module -Includes Function

# Find all modules with Role Capabilities
Find-Module -Includes RoleCapability

# Find all modules with the specified Role Capability name
Find-Module -RoleCapability RoleCap1
Find-Module -RoleCapability RoleCap2 -Includes RoleCapability

# Find modules with specific commands
Find-Module -Command Get-ScriptAnalyzerRule, Invoke-ScriptAnalyzer
Find-Module -Command Get-ScriptAnalyzerRule, Invoke-ScriptAnalyzer -Includes Cmdlet
Find-Module -Command Get-ScriptAnalyzerRule, Invoke-ScriptAnalyzer -Includes Function

# Find modules with -Filter based search. -Filter searches in description and names
Find-Module -Filter Cookbook
Find-Module -Filter RBAC
Find-Module -Filter 'App Domain' -Includes 'DscResource'

# Find all modules with tags Azure or DSC
Find-Module -Tag Azure, DSC

# Properties of Find-Module returned object
Find-Module AzureRM.Profile | Format-List * -Force

Name                       : AzureRM.profile
Version                    : 1.0.8
Type                       : Module
Description                : Microsoft Azure PowerShell - Profile credential management cmdlets for Azure Resource
                             Manager
Author                     : Microsoft Corporation
CompanyName                : {elogeel, azure-sdk}
Copyright                  : Microsoft Corporation. All rights reserved.
PublishedDate              : 5/4/2016 9:40:33 PM
InstalledDate              :
UpdatedDate                :
LicenseUri                 : https://raw.githubusercontent.com/Azure/azure-powershell/dev/LICENSE.txt
ProjectUri                 : https://github.com/Azure/azure-powershell
IconUri                    :
Tags                       : {PSModule}
Includes                   : {Function, RoleCapability, Command, DscResource...}
PowerShellGetFormatVersion :
ReleaseNotes               : https://github.com/Azure/azure-powershell/blob/dev/ChangeLog.md
Dependencies               : {}
RepositorySourceLocation   : https://www.powershellgallery.com/api/v2/
Repository                 : PSGallery
PackageManagementProvider  : NuGet
AdditionalMetadata         : {downloadCount, description, copyright, FileList...}

```


<!--HONumber=Oct16_HO1-->


