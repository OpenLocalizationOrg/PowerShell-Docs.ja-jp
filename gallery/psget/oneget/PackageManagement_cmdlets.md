# PackageManagement コマンドレット
これは、ソフトウェアの検出、インストール、およびインベントリ (SDII) をサポートする PackageManagement の中核となります。 次の操作のコマンドレットを試してください。
-   Find-Package
-   Find-PackageProvider
-   Get-Package
-   Get-PackageProvider
-   Get-PackageSource
-   Import-PackageProvider
-   Install-Package
-   Install-PackageProvider
-   Register-PackageSource
-   Save-Package
-   Set-PackageSource
-   Uninstall-Package
-   Unregister-PackageSource

PackageManagement は PowerShell モジュールであるため、次のことを行って PackageManagement 自体を更新できます。
```powershell
PS C:\> Install-Module PackageManagement –Force
```
この場合、PowerShell セッションを再入力して、PackageManagement の新しいバージョンに切り替える必要があります。

## [検索パッケージ コマンドレット](https://technet.microsoft.com/en-us/library/dn890709.aspx)
このコマンドレットでは、読み込まれたパッケージ プロバイダーを使用して、利用可能なパッケージ ソース内のソフトウェア パッケージを検出できます。
```powershell
# Find all available Windows PowerShell module packages from galleries registered
# with PowerShellGet provider
Find-Package -Provider PowerShellGet -Source PSGallery

# Find a package from a provider that is not yet installed
# This will bootstrap NuGet provider and then search for jquery package using NuGet
# with <http://www.nuget.org/api/v2/> as source
Find-Package -Name jquery –Provider NuGet -Source http://www.nuget.org/api/v2/

# Find package with name and version
# Here we are assuming that the user already registered nuget.org using
# Register-PackageSource. You can specify either the provider or the source, or
# neither. For the latter, performance may be less optimal as it searches through all
# the providers and registered sources.
Find-Package -Name jquery –Provider NuGet –RequiredVersion 2.1.4 -Source nuget.org
```

## [検索 PackageProvider コマンドレット](https://technet.microsoft.com/en-us/library/mt676544.aspx)
Find-PackageProvider コマンドレットは、PowerShellGet に登録されているパッケージ ソースで利用できる一致する PackageManagement プロバイダーを検索します。 これらは、Install-PackageProvider コマンドレットを使用したインストールに使用可能なパッケージ プロバイダーです。 既定では、これには 'PackageManagement' および 'Provider' タグの付いた PowerShell ギャラリーで利用できるモジュールが含まれます。 

Find-PackageProvider は、PackageManagement Azure BLOB ストアで利用できる一致する PackageManagement プロバイダーも検索します。このストアでは、これらを検索しインストールするために PackageManagement boostrapper プロバイダーを使用します。
```powershell
#Find all available package providers in PackageManagement azure blob store as well as in PowerShellGallery.com
Find-PackageProvider

#Find all versions of a provider
Find-PackageProvider -Name "Nuget" -AllVersions

#Find a provider from a specified source
Find-PackageProvider -Name "Gistprovider" -Source "PSGallery"

#For machines without internet, you can download the nuget provider first, put it to you file share and then use the following to install the nuget provider (TP5 or later).

Find-PackageProvider -Source  C:\sharedfolder\Providers\
Install-PackageProvider -Source C:\sharedfolder\Providers\ -Name nuget -force
    
```

## [Get パッケージ コマンドレット](https://technet.microsoft.com/en-us/library/dn890704.aspx)
このコマンドレットは、PackageManagement を使用してインストールされたすべてのソフトウェア パッケージの一覧を返します。
```powershell
# Get all the packages installed by Programs provider
Get-Package –Provider Programs

# Get all the packages installed by NuGet provider at c:\test using the dynamic
# parameter destination
Get-Package –Provider NuGet -Destination c:\test
```

## [Get PackageProvider コマンドレット](https://technet.microsoft.com/en-us/library/dn890703.aspx)
読み込まれ、ローカル コンピューターで使用できる状態のパッケージ プロバイダーは、このコマンドレットを使用してインベントリすることができます。
```powershell
# Get all currently loaded package providers
Get-PackageProvider

# The following cmdlet will show all the package providers available on the machine (including those that are not loaded):
Get-PackageProvider -ListAvailable
```

## [Get PackageSource コマンドレット](https://technet.microsoft.com/en-us/library/dn890705.aspx)
このコマンドレットは、パッケージ プロバイダーに登録されているパッケージ ソースの一覧を取得します。
```powershelll
# Get all package sources
Get-PackageSource

# Get all package sources for a specific provider
Get-PackageSource –ProviderName PowerShellGet
```

## [インポート PackageProvider コマンドレット](https://technet.microsoft.com/en-us/library/mt676545.aspx)
このコマンドレットは、PackageManagement パッケージ プロバイダーを現在のセッションに追加します。
```powershell
# Import a package provider from the local machine
Import-PackageProvider –Name MyProvider

#The -Name parameter can be either the name of the provider or the full path to the provider. Currently, we support .dll, .exe and.psm1 for the full path case. If the name of the provider is used for the -Name parameter, then additional version parameters such as -RequiredVersion, -MinimumVersion and -MaximumVersion may be specified. Otherwise, the latest version of the provider will be imported.

#If a package provider is not yet loaded to your system, we can discover and install on-demand. You can use explicit discovery and install cmdlets to do so:
 Find-PackageProvider
 Install-PackageProvider –Name MyProvider

#After installed, follow the Import-PackageProvider to load it to your system.

# Import a specific version of a package provider. PackageManagement supports installations of multiple versions of a package provider using PackageProvider cmdlets (not by bootstrapper provider). You can install another version of a package provider given that you already have one up running by:
Find-PackageProvider –Name "Nuget" -AllVersions
Install-PackageProvider -Name "Nuget" -RequiredVersion "2.8.5.201" -Force
Get-PackageProvider –ListAvailable
Import-PackageProvider –Name "Nuget" -RequiredVersion "2.8.5.201" -Verbose
Import-PackageProvider –Name MyProvider –RequiredVersion xxxx -force

As of the Windows Server Technical Preview(TP5), Install-PackageProvider does install as well as import the provider. Hence after you run find-packageprovider and install-packageprovider, the provider should be ready to use 
```

##[Install-Package コマンドレット](https://technet.microsoft.com/en-us/library/dn890711.aspx)

このコマンドレットでは、読み込まれたパッケージ プロバイダーを使用して、利用可能なパッケージ ソース内のソフトウェア パッケージをインストールできます。
```powershell
# Install a package by name.
# NuGet provider requires us to provide the dynamic parameter destination path
# when we use this provider to install. Not all providers will require you to supply
# dynamic parameters for PackageManagement cmdlets.
Install-Package -Name jquery -Source nuget.org -Destination c:\test

# Install a package by piping.
Find-Package -Name jquery –Provider NuGet | Install-Package -Destination c:\test
```

## [インストール PackageProvider コマンドレット](https://technet.microsoft.com/en-us/library/mt676543.aspx)
このコマンドレットは、1 つ以上の PackageManagement パッケージ プロバイダーをインストールします。
```powershell
# Install a package provider from the PowerShell Gallery
Install-PackageProvider –Name "Gistprovider" -Verbose

# Install a specified version of a package provider
Find-PackageProvider –Name "Nuget" -AllVersions
Install-PackageProvider -Name "Nuget" -RequiredVersion "2.8.5.201" -Force

# Find a provider and install it
Find-PackageProvider –Name "Gistprovider" | Install-PackageProvider -Verbose

# Install a provider to the current user’s module folder
Install-PackageProvider –Name Gistprovider –Verbose –Scope CurrentUser
```

## [レジスタ PackageSource コマンドレット](https://technet.microsoft.com/en-us/library/dn890701.aspx)
このコマンドレットは、指定したパッケージ プロバイダーのパッケージ ソースを追加します。
各 PackageManagement プロバイダーには、1 つまたは複数のソフトウェア ソースまたはリポジトリがある場合があります。 PackageManagement は、ソースを追加/削除/照会する PowerShell コマンドレットを提供します。 たとえば、NuGet プロバイダーのパッケージ ソースを登録できます。
```powershell
Register-PackageSource -Name "NugetSource" -Location "http://www.nuget.org/api/v2" –ProviderName nuget
```

## [保存パッケージ コマンドレット](https://technet.microsoft.com/en-us/library/dn890708.aspx)
このコマンドレットは、パッケージをインストールせずにローカル コンピューターに保存します。
```powershell
# Saves jquery package to c:\test using NuGetProvider
# Notes that the -Path parameter must point to an existing location
Save-Package -Name jquery –Provider NuGet -Path c:\test

# Save a package by piping.
Find-Package -Name jquery -Source http://www.nuget.org/api/v2/ | Save-Package -Path c:\test
Find-Package -source c:\test
```

## [セット PackageSource コマンドレット](https://technet.microsoft.com/en-us/library/dn890710.aspx)
このコマンドレットは、既存のパッケージ ソースに関する情報を変更します。 
```powershell
#Set-PackageSource changes the values for a source that has already been registered by running the Register-PackageSource cmdlet. By #running Set-PackageSource, you can change the source name and location.
Set-PackageSource  -Name nuget.org -Location  http://www.nuget.org/api/v2 -NewName nuget2 -NewLocation https://www.nuget.org/api/v2 
```

## [アンインストール パッケージ コマンドレット](https://technet.microsoft.com/en-us/library/dn890702.aspx)
このコマンドレットは、ローカル コンピューターにインストールされているパッケージをアンインストールします。
```powershell
# Uninstall jquery using nuget
Uninstall-Package -Name jquery –Provider NuGet -Destination c:\test

# Uninstall a package with by piping with Get-Package
Get-Package -Name jquery –Provider NuGet -Destination c:\test | Uninstall-Package
```

## [登録を解除 PackageSource コマンドレット](https://technet.microsoft.com/en-us/library/dn890707.aspx)
```powershell
# Unregister a package source for the NuGet provider. You can use command Unregister-PackageSource, to disconnect with a repository, and Get-PackageSource, to discover what the repositories are associated with that provider.
Unregister-PackageSource  -Name "NugetSource"
```



<!--HONumber=Oct16_HO1-->


