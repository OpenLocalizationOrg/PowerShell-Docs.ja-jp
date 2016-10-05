# Get-InstalledScript

取得は、スクリプトをコンピューターにインストールします。

## 説明

Get InstalledScript コマンドレットでは、コンピューターにインストールされている PowerShell スクリプトを取得します。

各インストールされているスクリプトは、Get InstalledScript は、インストールされているスクリプトをアンインストールするため、アンインストール スクリプトを必要に応じてパイプすることもできる PSRepositoryItemInfo オブジェクトを返します。

- Get InstalledScript は、インストールされているスクリプトの名前、バージョン パラメーターに基づいてフィルター処理できます。
- Get InstalledScript がバージョン パラメーターを使用してフィルター処理できます。 MinimumVersion、MaximumVersion、RequiredVersion、AllVersions です。
  - これらのパラメーターは MinmimumVersion と MaximumVersion を除く、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのスクリプト名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、Get InstalledScript は最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、スクリプトの最新のバージョンよりも大きいがインストールされているスクリプトの最新バージョンを返します。 
  - RequiredVersion パラメーターが指定されている場合、指定したバージョンを完全に一致するインストール済みのスクリプトのバージョンは Get InstalledScript だけを返します。

## コマンドレット構文

```powershell
Get-Command -Name Get-InstalledScript -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[Get InstalledScript](http://go.microsoft.com/fwlink/?LinkId=619790)

## コマンド例

```powershell

# Get all scripts installed using PowerShellGet cmdlets
Get-InstalledScript

# Get a specific installed script
Get-InstalledScript Show-Tree

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.0.0      Show-Tree                           PSGallery            Script to show the layout of PowerShell namespaces (Tr...

# Get installed script with wildcards
Get-InstalledScript -Name *Azure*

# Get all versions of an installed script
Get-InstalledScript -Name Connect-O365 -AllVersions

# Get installed script with MinimumVersion
Get-InstalledScript -Name Connect-O365 -MinimumVersion 1.1

# Get installed script with MaximumVersion
Get-InstalledScript -Name Connect-O365 -MaximumVersion 1.6.3

# Get installed script with version range
Get-InstalledScript -Name Connect-O365 -MinimumVersion 1.1 -MaximumVersion 1.6.3

# Get installed script with RequiredVersion
Get-InstalledScript -Name Connect-O365 -RequiredVersion 1.4

# Properties of Get-InstalledScript returned object
Get-InstalledScript Show-Tree | Format-List * -Force

Name                       : Show-Tree
Version                    : 1.0.0
Type                       : Script
Description                : Script to show the layout of PowerShell namespaces (Trees) using ASCII
Author                     : Jeffrey Snover
CompanyName                : jsnover
Copyright                  : (C) Microsoft Corporation. All rights reserved.
PublishedDate              : 2/15/2016 10:15:35 PM
InstalledDate              : 5/4/2016 11:44:13 PM
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
AdditionalMetadata         : {description, installeddate, tags, PackageManagementProvider...}
InstalledLocation          : C:\Program Files\WindowsPowerShell\Scripts


```

<!--HONumber=Oct16_HO1-->


