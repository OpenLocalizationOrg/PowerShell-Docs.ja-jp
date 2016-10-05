# Get-InstalledModule

取得は、コンピューターにモジュールをインストールします。

## 説明

Get InstalledModule コマンドレットは、インストール モジュールのコマンドレットを使用してインストールされたコンピューターにインストールされている PowerShell モジュールを取得します。

各インストールされているモジュールは、Get InstalledModule は、インストールされているモジュールをアンインストールするため、アンインストール モジュールに必要に応じてパイプすることもできる PSRepositoryItemInfo オブジェクトを返します。

- Get InstalledModule は、インストールされているモジュールの名前、バージョン パラメーターに基づいてフィルター処理できます。
- Get InstalledModule がバージョン パラメーターを使用してフィルター処理できます。 MinimumVersion、MaximumVersion、RequiredVersion、AllVersions です。
  - これらのパラメーターは MinmimumVersion と MaximumVersion を除く、相互に排他的です。
  - これらのバージョン パラメーターは、任意のワイルドカードを含まない 1 つのモジュール名のみで許可されます。
  - RequiredVersion パラメーターが指定されていない場合、Get InstalledModule は最低限のバージョンが指定されていない場合、同じかまたは指定された最小のバージョンや、モジュールの最新のバージョンよりも大きいはインストールされたモジュールの最新バージョンを返します。 
  - RequiredVersion パラメーターが指定されている場合、指定したバージョンを完全に一致するインストール済みのモジュールのバージョンは Get InstalledModule だけを返します。

## コマンドレット構文
```powershell
Get-Command -Name Get-InstalledModule -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[Get InstalledModule](http://go.microsoft.com/fwlink/?LinkId=526863)

## コマンド例

```powershell

# Get all modules installed using PowerShellGet cmdlets
Get-InstalledModule

# Get a specific installed module
Get-InstalledModule DJoin

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.0        DJoin                               PSGallery            This is a PowerShell frontend to the DJOIN.exe c...

# Get installed module with wildcards
Get-InstalledModule -Name AzureRM*

# Get all versions of an installed module
Get-InstalledModule -Name AzureRM.Automation -AllVersions

# Get installed module with MinimumVersion
Get-InstalledModule -Name AzureRM.Automation -MinimumVersion 1.0.0

# Get installed module with MaximumVersion
Get-InstalledModule -Name AzureRM.Automation -MaximumVersion 1.0.8

# Get installed module with version range
Get-InstalledModule -Name AzureRM.Automation -MinimumVersion 1.0.0 -MaximumVersion 1.0.8

# Get installed module with RequiredVersion
Get-InstalledScript -Name AzureRM.Automation -RequiredVersion 1.0.3

# Properties of Get-InstalledModule returned object
Get-InstalledModule DJoin | Format-List * -Force

Name                       : DJoin
Version                    : 1.0
Type                       : Module
Description                : This is a PowerShell frontend to the DJOIN.exe command which provides better
                             discoverability and usability.
Author                     : Jeffrey Snover
CompanyName                : jsnover
Copyright                  : (C) Microsoft Corporation. All rights reserved.
PublishedDate              : 2/15/2016 7:12:37 PM
InstalledDate              : 4/5/2016 4:13:39 PM
UpdatedDate                :
LicenseUri                 :
ProjectUri                 :
IconUri                    :
Tags                       : {Nano, PSModule}
Includes                   : {Function, RoleCapability, Command, DscResource...}
PowerShellGetFormatVersion :
ReleaseNotes               :
Dependencies               : {}
RepositorySourceLocation   : https://www.powershellgallery.com/api/v2/
Repository                 : PSGallery
PackageManagementProvider  : NuGet
AdditionalMetadata         : {description, installeddate, tags, PackageManagementProvider...}
InstalledLocation          : C:\Program Files\WindowsPowerShell\Modules\DJoin\1.0

```



## PSGetRepositoryItemInfo オブジェクトで InstalledDate と UpdatedDate プロパティ

    During the install operation:
        InstalledDate: current DateTime (Get-Date) value
        UpdatedDate: null

    During the Update operation:
        InstalledDate: InstalledDate from the previous installation otherwise current DateTime (Get-Date) value
        UpdatedDate: current DateTime (Get-Date) value

```powershell
Install-Module -Name ContosoServer -RequiredVersion 1.0 -Repository INT
Get-InstalledModule -Name ContosoServer | Format-Table Name, InstalledDate, UpdatedDate

Name          InstalledDate         UpdatedDate
----          -------------         -----------
ContosoServer 2/29/2016 11:59:14 AM


\Update-Module -Name ContosoServer
Get-InstalledModule -Name ContosoServer | Format-Table Name, InstalledDate, UpdatedDate

Name          InstalledDate         UpdatedDate
----          -------------         -----------
ContosoServer 2/29/2016 11:59:14 AM 2/29/2016 12:00:15 PM
```

<!--HONumber=Oct16_HO1-->


