# Install-Module

オンライン リポジトリからの PowerShell モジュールをローカルのコンピューターにインストールします。

## 説明

インストール モジュールのコマンドレットでは、オンライン ギャラリーから 1 つまたは複数のモジュールをダウンロード、検証し、指定したインストールのスコープには、ローカルのコンピューターにインストールします。

インストール モジュール コマンドレットは、オンライン ギャラリーから指定の条件を満たす 1 つまたは複数のモジュールを取得、検索結果が有効なモジュールとモジュール フォルダーをコピーのインストール場所にいることを確認します。

スコープが定義されていない場合、またはスコープのパラメーターの値が AllUsers である場合は、モジュールは %systemdrive%:\Program、\windowspowershell\modules にインストールされます。 スコープの値は、CurrentUser は、$home \documents\windowspowershell\modules にモジュールがインストールされます。

指定したモジュールの最小値と正確なバージョンに基づいて、結果をフィルター処理することができます。

- Windows PowerShell 5.0 以降の Side-by-Side バージョン サポート
- モジュールの依存関係のインストール サポート
- **Prompt、信頼されていない:**ユーザー受け入れは信頼されていないリポジトリからモジュールをインストールするために必要です。
- フォース攻撃には、インストールされているモジュールが再インストールされます。
- RequiredVersion では、powershell バージョン 5.0 以降の既存のバージョンで SxS で指定されたバージョンをインストールします。

### スコープ
モジュールのインストールのスコープを指定します。 このパラメーターの使用可能な値: AllUsers と CurrentUser です。

既定のインストールのスコープは、AllUsers です。

AllUsers スコープにより、モジュールで、コンピューターのすべてのユーザーにアクセス可能な場所にインストールする"$env:path: \program、\windowspowershell\modules"です。

CurrentUser スコープでは、モジュールを現在のユーザーにのみ使用できるようにモジュール"$home \documents\windowspowershell\modules"にのみインストールすることができます。

## メモ

このコマンドレットは、Windows PowerShell 3.0 または Windows 7 または Windows 2008 R2 および Windows の今後のリリースでの Windows PowerShell の今後のリリースを実行します。

(があるない、.psm1、.psd1、または .dll、同じ名前のフォルダー内) の場合は、インストールされているモジュールをインポートできない場合、コマンドに、Force パラメーターを追加する場合にのみ、インストールが失敗します。

コンピューター上のモジュールのバージョンには、Name パラメーターに指定された値と一致する MinimumVersion または RequiredVersion パラメーターを追加していない場合は、そのモジュールをインストールしなくても何も行わずにモジュールのインストールが続行されます。 MinimumVersion または RequiredVersion パラメーターが指定された、既存のモジュールがそのパラメーターの値と一致しない場合は、エラーが発生します。 もっと詳しく指定する: 現在インストールされているモジュールのバージョンが MinimumVersion パラメーターの値よりも低いまたは RequiredVersion パラメーターの値に等しくない場合は、エラーが発生します。 インストールされているモジュールのバージョンが MinimumVersion パラメーターの値より大きいまたは RequiredVersion パラメーターの値と等しい場合は、そのモジュールをインストールしなくても何も行わずにモジュールのインストールが続行されます。

指定した名前に一致するオンライン ギャラリーでモジュールが存在しない場合、インストール モジュールはエラーを返します。

複数のモジュールをインストールするには、コンマで区切られた、モジュール名の配列を指定します。 複数のモジュール名を指定する場合は、MinimumVersion または RequiredVersion を追加することはできません。

既定では、モジュールは、Windows PowerShell Desired State Configuration (DSC) のリソースをインストールするときに混乱を避けるため、Program Files フォルダーにインストールされます。モジュールのインストール; に複数の PSGetItemInfo オブジェクトをパイプすることができます。これは、複数のモジュールを指定する 1 つのコマンドでインストールする別の方法です。

インストールされている、悪意のあるコードが含まれる実行中のモジュールを防止するには、モジュールは自動的にインポートされませんのインストールによって。 セキュリティ ベスト プラクティスとして、最初に、モジュールのすべてのコマンドレットと関数を実行する前にモジュールのコードを評価します。


## コマンドレット構文
```powershell
Get-Command -Name Install-Module -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[モジュールのインストール](http://go.microsoft.com/fwlink/?LinkID=398573)

## コマンド例

```powershell

# Install a module by name
Install-Module -Name MyDscModule

# Install multiple modules
Install-Module ContosoClient,ContosoServer

# Install a module using its minimum version
Install-Module -Name ContosoServer -MinimumVersion 1.0

# Install a specific version of a module
Install-Module -Name ContosoServer -RequiredVersion 1.1.3

# Install the latest version of a module to $home\Documents\WindowsPowerShell\Modules.
Install-Module -Name ContosoServer -Scope CurrentUser

# if a module is already available under $env:PSModulePath, below command fails with 'ModuleAlreadyInstalled,Install-Package,Microsoft.PowerShell.PackageManagement.Cmdlets.InstallPackage'
Install-Module ContosoServer -RequiredVersion 1.5

# if a module is already available under $env:PSModulePath, below command fails with 'ModuleAlreadyInstalled,Install-Package,Microsoft.PowerShell.PackageManagement.Cmdlets.InstallPackage'
Install-Module ContosoServer -MinimumVersion 2.5

# Install multiple modules from multiple registered repositories
Install-Module ContosoClient,ContosoServer -Repository PSGallery, PrivatePSGallery

# Install a module with -WhatIf
Install-Module ContosoClient -WhatIf

# Install a module with -Confirm. A prompt will be displayed to confirm the installation.
Install-Module ContosoClient -WhatIf

# -Force option reinstalls the installed module
Install-Module ContosoClient -Force

# Install a module with dependencies
Install-Module -Name 
```

## パイプライン処理でインストール モジュールのコマンドレット

```powershell

# Find a module and install it
Find-Module -Name "MyDSC*" | Install-Module

# Find a module and install it to the CurrentUser scope
Find-Module -Name "MyDSC*" | Install-Module -Scope CurrentUser

# Find commands by name and install them
# The first command finds the specified commands in the INT repository, and then uses the pipeline operator to pass them to Install-Module to install them.
# The second command uses Get-InstalledModule to verify the modules from the prior command are installed.
Find-Command -Repository "INT" -Name Get-ContosoClient,Get-ContosoServer | Install-Module
Get-InstalledModule

# This command finds the resource named MyResource and passes it to the Install-Module cmdlet by using the pipeline operator. The Install-Module cmdlet installs the module for the resource. 
# If you pipe multiple resources to the Install-Module cmdlet from the same module, Install-Module attempts to install the module only once. 
Find-DscResource -Name "MyResource" | Install-Module
Get-InstalledModule

# Find multiple role capabilities and install them
Find-RoleCapability -Name MyJeaRole, Maintenance | Install-Module
Get-InstalledModule

```

## PowerShell 5.0 以降の Side-by-Side バージョン サポート

PowerShellGet は、インストール モジュールでのサイド バイ サイド (SxS) モジュールのバージョンのサポートをサポートしている更新プログラムのモジュール、および Windows PowerShell 5.0 以降を実行している発行モジュールのコマンドレットです。

### モジュールのインストール例

```powershell
# Install a version of the module
Install-Module -Name PSScriptAnalyzer -RequiredVersion 1.1.0 -Repository PSGallery
Get-Module -ListAvailable -Name PSScriptAnalyzer | Format-List Name,Version,ModuleBase

Name : PSScriptAnalyzer
Version : 1.1.0
ModuleBase : C:\Program Files\WindowsPowerShell\Modules\PSScriptAnalyzer\1.1.0

# Install another version of the module in Side-by-Side with already installed version.
Install-Module -Name PSScriptAnalyzer -RequiredVersion 1.1.1 -Repository PSGallery
Get-Module -ListAvailable -Name PSScriptAnalyzer | Format-List Name,Version,ModuleBase

Name       : PSScriptAnalyzer 
Version    : 1.1.1
ModuleBase : C:\Program Files\WindowsPowerShell\Modules\PSScriptAnalyzer\1.1.1
Name       : PSScriptAnalyzer
Version    : 1.1.0
ModuleBase : C:\Program Files\WindowsPowerShell\Modules\PSScriptAnalyzer\1.1.0

# Get all versions of an installed module
Get-InstalledModule -Name PSScriptAnalyzer -AllVersions
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.1.0      PSScriptAnalyzer                    PSGallery            PSScriptAnalyzer provides script analysis... 
1.1.1      PSScriptAnalyzer                    PSGallery            PSScriptAnalyzer provides script analysis...


```

## 依存関係と共にモジュールをインストールします。

```powershell

# Find a module
Find-Module -Name TypePx -Repository PSGallery

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.1.20   TypePx                              PSGallery            The TypePx module adds properties and methods to the m...

# Find a module and its dependencies
Find-Module -Name TypePx -Repository PSGallery -IncludeDependencies

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.1.20   TypePx                              PSGallery            The TypePx module adds properties and methods to the m...
1.0.5.18   SnippetPx                           PSGallery            The SnippetPx module enhances the snippet experience i...

# Discover the dependencies list without adding -IncludeDependencies
$result = Find-Module -Name TypePx -Repository PSGallery
$result.Dependencies

Name                           Value
----                           -----
Name                           SnippetPx
CanonicalId                    powershellget:SnippetPx/#https://www.powershellgallery.com/api/v2/


# Now install the module along with its dependencies
Install-Module -Name TypePx -Repository PSGallery -Verbose

VERBOSE: Repository details, Name = 'PSGallery', Location = 'https://www.powershellgallery.com/api/v2/'; IsTrusted =
'False'; IsRegistered = 'True'.
VERBOSE: Using the provider 'PowerShellGet' for searching packages.
VERBOSE: Using the specified source names : 'PSGallery'.
VERBOSE: Getting the provider object for the PackageManagement Provider 'NuGet'.
VERBOSE: The specified Location is 'https://www.powershellgallery.com/api/v2/' and PackageManagementProvider is
'NuGet'.
VERBOSE: Searching repository 'https://www.powershellgallery.com/api/v2/FindPackagesById()?id='TypePx'' for ''.
VERBOSE: Total package yield:'1' for the specified package 'TypePx'.
VERBOSE: Performing the operation "Install-Module" on target "Version '2.0.1.20' of module 'TypePx'".

Untrusted repository
You are installing the modules from an untrusted repository. If you trust this repository, change its
InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
'PSGallery'?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
VERBOSE: The installation scope is specified to be 'AllUsers'.
VERBOSE: The specified module will be installed in 'C:\Program Files\WindowsPowerShell\Modules'.
VERBOSE: The specified Location is 'NuGet' and PackageManagementProvider is 'NuGet'.
VERBOSE: Downloading module 'TypePx' with version '2.0.1.20' from the repository
'https://www.powershellgallery.com/api/v2/'.
VERBOSE: Searching repository 'https://www.powershellgallery.com/api/v2/FindPackagesById()?id='TypePx'' for ''.
VERBOSE: Searching repository 'https://www.powershellgallery.com/api/v2/FindPackagesById()?id='SnippetPx'' for ''.
VERBOSE: InstallPackage' - name='SnippetPx',
version='1.0.5.18',destination='C:\Users\manikb\AppData\Local\Temp\1027042896'
VERBOSE: DownloadPackage' - name='SnippetPx',
version='1.0.5.18',destination='C:\Users\manikb\AppData\Local\Temp\1027042896\SnippetPx\SnippetPx.nupkg',
uri='https://www.powershellgallery.com/api/v2/package/SnippetPx/1.0.5.18'
VERBOSE: Downloading 'https://www.powershellgallery.com/api/v2/package/SnippetPx/1.0.5.18'.
VERBOSE: Completed downloading 'https://www.powershellgallery.com/api/v2/package/SnippetPx/1.0.5.18'.
VERBOSE: Completed downloading 'SnippetPx'.
VERBOSE: Hash for package 'SnippetPx' does not match hash provided from the server.
VERBOSE: InstallPackageLocal' - name='SnippetPx',
version='1.0.5.18',destination='C:\Users\manikb\AppData\Local\Temp\1027042896'
VERBOSE: InstallPackage' - name='TypePx',
version='2.0.1.20',destination='C:\Users\manikb\AppData\Local\Temp\1027042896'
VERBOSE: DownloadPackage' - name='TypePx',
version='2.0.1.20',destination='C:\Users\manikb\AppData\Local\Temp\1027042896\TypePx\TypePx.nupkg',
uri='https://www.powershellgallery.com/api/v2/package/TypePx/2.0.1.20'
VERBOSE: Downloading 'https://www.powershellgallery.com/api/v2/package/TypePx/2.0.1.20'.
VERBOSE: Completed downloading 'https://www.powershellgallery.com/api/v2/package/TypePx/2.0.1.20'.
VERBOSE: Completed downloading 'TypePx'.
VERBOSE: Hash for package 'TypePx' does not match hash provided from the server.
VERBOSE: InstallPackageLocal' - name='TypePx',
version='2.0.1.20',destination='C:\Users\manikb\AppData\Local\Temp\1027042896'
VERBOSE: Installing the dependency module 'SnippetPx' with version '1.0.5.18' for the module 'TypePx'.
VERBOSE: Module 'SnippetPx' was installed successfully to path 'C:\Program
Files\WindowsPowerShell\Modules\SnippetPx\1.0.5.18'.
VERBOSE: Module 'TypePx' was installed successfully to path 'C:\Program
Files\WindowsPowerShell\Modules\TypePx\2.0.1.20'.


# Get the installed modules
Get-InstalledModule

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
1.0.5.18   SnippetPx                           PSGallery            The SnippetPx module enhances the snippet experience i...
2.0.1.20   TypePx                              PSGallery            The TypePx module adds properties and methods to the m...

```

## エラーのシナリオ

```powershell

# Below command fails with 'NameShouldNotContainWildcardCharacters,Install-Module'
Install-Module ContosoServe*

# Below command fails with 'VersionRangeAndRequiredVersionCannotBeSpecifiedTogether,Install-Module'
Install-Module ContosoServer -MinimumVersion 1.0 -RequiredVersion 5.0

# Below command fails with 'VersionParametersAreAllowedOnlyWithSingleName,Install-Module'
Install-Module ContosoClient,ContosoServer -RequiredVersion 2.0

# Below command fails with 'VersionParametersAreAllowedOnlyWithSingleName,Install-Module'
Install-Module ContosoClient,ContosoServer -MinimumVersion 2.0

```


<!--HONumber=Oct16_HO1-->


