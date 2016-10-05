# モジュール管理用の PowerShellGet コマンドレット

- [検索 DscResource](https://technet.microsoft.com/en-us/library/mt654006.aspx)
- [モジュールの検索](https://technet.microsoft.com/en-us/library/dn807167.aspx)
- [検索スクリプト](https://technet.microsoft.com/en-us/library/mt654001.aspx)
- [Get InstalledModule](https://technet.microsoft.com/en-us/library/mt653990.aspx)
- [Get InstalledScript](https://technet.microsoft.com/en-us/library/mt653994.aspx)
- [Get PSRepository](https://technet.microsoft.com/en-us/library/dn807170.aspx)
- [モジュールのインストール](https://technet.microsoft.com/en-us/library/dn807162.aspx)
- [インストール スクリプト](https://technet.microsoft.com/en-us/library/mt653998.aspx)
- [新しい ScriptFileInfo](https://technet.microsoft.com/en-us/library/mt653995.aspx)
- [発行モジュール](https://technet.microsoft.com/en-us/library/dn807163.aspx)
- [公開スクリプト](https://technet.microsoft.com/en-us/library/mt654003.aspx)
- [レジスタ PSRepository](https://technet.microsoft.com/en-us/library/dn807168.aspx)
- [保存モジュール](https://technet.microsoft.com/en-us/library/mt653992.aspx)
- [スクリプトの保存](https://technet.microsoft.com/en-us/library/mt654004.aspx)
- [セット PSRepository](https://technet.microsoft.com/en-us/library/dn807165.aspx)
- [テスト ScriptFileInfo](https://technet.microsoft.com/en-us/library/mt654005.aspx)
- [アンインストール モジュール](https://technet.microsoft.com/en-us/library/mt653996.aspx)
- [アンインストール スクリプト](https://technet.microsoft.com/en-us/library/mt653989.aspx)
- [更新プログラム モジュール](https://technet.microsoft.com/en-us/library/dn807166.aspx)
- [更新プログラム ModuleManifest](https://technet.microsoft.com/en-us/library/mt654002.aspx)
- [更新スクリプト](https://technet.microsoft.com/en-us/library/mt653997.aspx)
- [更新プログラム ScriptFileInfo](https://technet.microsoft.com/en-us/library/mt653991.aspx)
- [登録を解除 PSRepository](https://technet.microsoft.com/en-us/library/dn807161.aspx)

## モジュールの依存関係のインストール サポート、Get-InstalledModule および Uninstall-Module コマンドレット
- モジュールの依存関係の作成が Publish-Module コマンドレットに追加されました。 PSModuleInfo の RequiredModules および NestedModules リストは、発行するモジュールの依存関係リストの準備で使用されます。
- 依存関係のインストール サポートが Install-Module および Update-Module コマンドレットに追加されました。 モジュールの依存関係が既定でインストールされ、更新されます。
- モジュールの依存関係を結果に含める -IncludeDependencies パラメーターが Find-Module コマンドレットに追加されました。
- Find-Module、Install-Module、および Update-Module コマンドレットに -MaximumVersion サポートが追加されました。
- 新しい Get-InstalledModule および Uninstall-Module コマンドレットが追加されました。

## モジュールの依存関係サポートを含む PowerShellGet コマンドレットのデモ:

### モジュールの依存関係がリポジトリで使用できることを確認します。
```powershell
Find-Module -Repository LocalRepo -Name RequiredModule1,RequiredModule2,RequiredModule3,NestedRequiredModule1,NestedRequiredModule2,NestedRequiredModule3 | Sort-Object -Property Name

Version    Name                     Repository    Description                  
-------    ----                     ----------    -----------                  
2.5        NestedRequiredModule1    LocalRepo     NestedRequiredModule1 module 
2.5        NestedRequiredModule2    LocalRepo     NestedRequiredModule2 module 
2.0        NestedRequiredModule3    LocalRepo     NestedRequiredModule3 module 
2.5        RequiredModule1          LocalRepo     RequiredModule1 module  
2.5        RequiredModule2          LocalRepo     RequiredModule2 module  
2.0        RequiredModule3          LocalRepo     RequiredModule3 module
```

### モジュール マニフェストの RequiredModules および NestedModules プロパティで指定されている依存関係があるモジュールを作成します。
```powershell
$RequiredModules = @('RequiredModule1',
                     @{ModuleName = 'RequiredModule2'; ModuleVersion = '1.5'; },
                     @{ModuleName = 'RequiredModule3'; RequiredVersion = '2.0'; })

$NestedRequiredModules = @('TestDepWithNestedRequiredModules1.psm1', 'NestedRequiredModule1',
                            @{ModuleName = 'NestedRequiredModule2'; ModuleVersion = '1.5'; },
                            @{ModuleName = 'NestedRequiredModule3'; RequiredVersion = '2.0'; })

New-ModuleManifest -Path 'C:\Program Files\WindowsPowerShell\Modules\TestDepWithNestedRequiredModules1\TestDepWithNestedRequiredModules1.psd1' `
-NestedModules $NestedRequiredModules -RequiredModules $RequiredModules -ModuleVersion "1.0" -Description "TestDepWithNestedRequiredModules1 module"
```

###  依存関係がある TestDepWithNestedRequiredModules1 モジュールの 2 つのバージョン (**"1.0"** と **"2.0"**) をリポジトリに発行します。
```powershell
Publish-Module -Name TestDepWithNestedRequiredModules1 -Repository LocalRepo -NuGetApiKey "MyNuGet-ApiKey-For-LocalRepo"
```

###  -IncludeDependencies を指定して、TestDepWithNestedRequiredModules1 モジュールを依存関係と共に検索します。
```powershell
Find-Module -Name TestDepWithNestedRequiredModules1 -Repository LocalRepo –IncludeDependencies -MaximumVersion "1.0"

Version    Name                                Repository  Description 
-------    ----                                ----------  -----------  
1.0        TestDepWithNestedRequiredModules1   LocalRepo   TestDepWithNestedRequiredModules1 module  
2.5        RequiredModule1                     LocalRepo   RequiredModule1 module      
2.5        RequiredModule2                     LocalRepo   RequiredModule2 module      
2.0        RequiredModule3                     LocalRepo   RequiredModule3 module      
2.5        NestedRequiredModule1               LocalRepo   NestedRequiredModule1 module
2.5        NestedRequiredModule2               LocalRepo   NestedRequiredModule2 module
2.0        NestedRequiredModule3               LocalRepo   NestedRequiredModule3 module
``` 

### Find-Module メタデータを使用してモジュールの依存関係を検索します。
```powershell
$psgetModuleInfo = Find-Module -Repository MSPSGallery -Name ModuleWithDependencies2
$psgetModuleInfo.Dependencies.ModuleName

RequiredModule1
RequiredModule2
RequiredModule3
NestedRequiredModule1
NestedRequiredModule2
NestedRequiredModule3

$psgetModuleInfo.Dependencies

Name Value
---- -----
ModuleName RequiredModule1
CanonicalId PowerShellGet:RequiredModule1/#http://psget/psGallery/api/v2/

ModuleName RequiredModule2
ModuleVersion 2.0
CanonicalId PowerShellGet:RequiredModule2/2.0#http://psget/psGallery/api/v2/

ModuleName RequiredModule3
RequiredVersion 2.5
CanonicalId PowerShellGet:RequiredModule3/2.5#http://psget/psGallery/api/v2/

ModuleName NestedRequiredModule1
CanonicalId PowerShellGet:NestedRequiredModule1/#http://psget/psGallery/api/v2/

ModuleName NestedRequiredModule2
ModuleVersion 2.0
CanonicalId PowerShellGet:NestedRequiredModule2/2.0#http://psget/psGallery/api/v2/

ModuleName NestedRequiredModule3
RequiredVersion 2.5
CanonicalId PowerShellGet:NestedRequiredModule3/2.5#http://psget/psGallery/api/v2/
```

###  依存関係を持つ TestDepWithNestedRequiredModules1 モジュールをインストールします。
```powershell
Install-Module -Name TestDepWithNestedRequiredModules1 -Repository LocalRepo -RequiredVersion "1.0"
Get-InstalledModule

Version    Name                    Repository   Description                 
-------    ----                    ----------   -----------                 
1.0        NestedRequiredModule1   LocalRepo    NestedRequiredModule1 module
2.5        NestedRequiredModule2   LocalRepo    NestedRequiredModule2 module
2.0        NestedRequiredModule3   LocalRepo    NestedRequiredModule3 module
1.0        RequiredModule1         LocalRepo    RequiredModule1 module      
2.5        RequiredModule2                    LocalRepo    RequiredModule2 module 
2.0        RequiredModule3                    LocalRepo    RequiredModule3 module 
1.0        TestDepWithNestedRequiredModules1  LocalRepo    TestDepWithNestedRequiredModules1 module
```

###  依存関係を持つ TestDepWithNestedRequiredModules1 モジュールを更新します。
```powershell
Find-Module -Name TestDepWithNestedRequiredModules1 -Repository LocalRepo -AllVersions

Version    Name                                Repository  Description
-------    ----                                ----------  -----------
1.0        TestDepWithNestedRequiredModules1   LocalRepo   TestDepWithNestedRequiredModules1 module
2.0        TestDepWithNestedRequiredModules1   LocalRepo   TestDepWithNestedRequiredModules1 module

Update-Module -Name TestDepWithNestedRequiredModules1 -RequiredVersion 2.0
Get-InstalledModule

Version    Name                                Repository  Description
-------    ----                                ----------  -----------
1.0        NestedRequiredModule1               LocalRepo   NestedRequiredModule1 module
2.5        NestedRequiredModule2               LocalRepo   NestedRequiredModule2 module
2.0        NestedRequiredModule3               LocalRepo   NestedRequiredModule3 module
2.5        NestedRequiredModule3               LocalRepo   NestedRequiredModule3 module
1.0        RequiredModule1                     LocalRepo   RequiredModule1 module
2.5        RequiredModule2                     LocalRepo   RequiredModule2 module
2.0        RequiredModule3                     LocalRepo   RequiredModule3 module
2.5        RequiredModule3                     LocalRepo   RequiredModule3 module
1.0        TestDepWithNestedRequiredModules1   LocalRepo   TestDepWithNestedRequiredModules1 module
2.0        TestDepWithNestedRequiredModules1   LocalRepo   TestDepWithNestedRequiredModules1 module
```

###  Uninstall-Module コマンドレットを実行して、PowerShellGet を使用してインストールしたモジュールをアンインストールします。
削除するモジュールに他のモジュールが依存する場合、PowerShellGet はエラーをスローします。
```powershell
Get-InstalledModule -Name RequiredModule1 | Uninstall-Module

PackageManagement\Uninstall-Package : The module 'RequiredModule1' of version '2.5' in module base folder 'C:\Program Files\WindowsPowerShell\Modules\RequiredModule1\2.5' cannot be uninstalled, because one or more other modules 'ModuleWithDependencies2' are dependent on this module. Uninstall the modules that depend on this module before uninstalling module 'RequiredModule1'.
At C:\Program Files\WindowsPowerShell\Modules\PowerShellGet\PSGet.psm1:1303 char:25
+ ... $null = PackageManagement\\Uninstall-Package @PSBoundParameters
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (Microsoft.Power...ninstallPackage:UninstallPackage) [Uninstall-Package], Exception
+ FullyQualifiedErrorId : UnableToUninstallAsOtherModulesNeedThisModule,Uninstall-Package,Microsoft.PowerShell.PackageManagement.Cmdlets.UninstallPackage
```

## Save-Module コマンドレット
```powershell
Save-Module -Repository MSPSGallery -Name ModuleWithDependencies2 -Path C:\MySavedModuleLocation
dir C:\MySavedModuleLocation

Directory: C:\MySavedModuleLocation

Mode LastWriteTime Length Name
---- ------------- ------ ----
d----- 4/21/2015 5:40 PM ModuleWithDependencies2
d----- 4/21/2015 5:40 PM NestedRequiredModule1
d----- 4/21/2015 5:40 PM NestedRequiredModule2
d----- 4/21/2015 5:40 PM NestedRequiredModule3
d----- 4/21/2015 5:40 PM RequiredModule1
d----- 4/21/2015 5:40 PM RequiredModule2
d----- 4/21/2015 5:40 PM RequiredModule3
```

## Update-ModuleManifest コマンドレット
この新しいコマンドレットを使用して、入力プロパティ値でマニフェスト ファイルを更新します。 このコマンドレットは、Test-ModuleManifest が受け取るすべてのパラメーターを受け取ります。

多数のモジュール作成者は FunctionsToExport、CmdletsToExport などのエクスポートされた値に "\ *" を指定します。PowerShell ギャラリーへのモジュールの発行時に、指定されていない関数やコマンドはギャラリーに正しく設定されません。 このため、モジュール作成者はマニフェストを適切な値で更新することをお勧めします。

プロパティをエクスポートしたモジュールがある場合は、Update-ModuleManifest によって、エクスポートされた関数、コマンドレット、変数などの情報を指定されたマニフェスト ファイルに入力します。
```powershell
Get-Content -Path "C:\Temp\PSGTEST-TestPackageMetadata\2.5\PSGTEST-TestPackageMetadata.psd1"
@{
# Script module or binary module file associated with this manifest.
# RootModule = ''
# Version number of this module.
ModuleVersion = '2.5'
# ID used to uniquely identify this module
GUID = '610e5c5b-dc42-4eaa-8511-ebfb44066d5e'

#(Other properties removed here for Simplicity…)

# Functions to export from this module
FunctionsToExport = '*'
# Cmdlets to export from this module
CmdletsToExport = '*'
# Variables to export from this module
VariablesToExport = '*'
# Aliases to export from this module
AliasesToExport = '*'
}
```

Update-ModuleManifest の後:
```powershell
Update-ModuleManifest -Path "C:\Temp\PSGTEST-TestPackageMetadata\2.5\PSGTEST-TestPackageMetadata.psd1"
Get-Content -Path "C:\Temp\PSGTEST-TestPackageMetadata\2.5\PSGTEST-TestPackageMetadata.psd1"
#
# Module manifest for module 'NewManifest'
#
# Generated by: author name
#
# Generated on: 11/13/2015
#
@{
# Script module or binary module file associated with this manifest.
# RootModule = ''
# Version number of this module.
ModuleVersion = '2.5'
# ID used to uniquely identify this module
GUID = '610e5c5b-dc42-4eaa-8511-ebfb44066d5e'
# Functions to export from this module
FunctionsToExport = 'Get-FooFn Get-FooWF'
# Cmdlets to export from this module
CmdletsToExport = 'Test-PSGetTestCmdlet'
}
```

各モジュールに、関連付けられているメタデータ フィールドもあります。 PowrShell ギャラリーでメタデータを正しく表示するためには、Update-ModuleManifest を使用して PrivateData でこれらのフィールドに入力できます。
```powershell
Update-ModuleManifest -Path "C:\Temp\PSGTEST-TestPackageMetadata\2.5\PSGTEST-TestPackageMetadata.psd1" -Tags "Tag1" -LicenseUri "http://license.com" -ProjectUri "http://project.com" -IconUri "http://icon.com" -ReleaseNotes "Test module"
```
マニフェスト ファイル テンプレートからの PrivateData ハッシュ テーブルには、次のプロパティがあります。
```powershell
# Private data to pass to the module specified in RootModule/ModuleToProcess. This may also contain a PSData hashtable with additional module metadata used by PowerShell.
PrivateData = @{
    PSData = @{
        # Tags applied to this module. These help with module discovery in online galleries.
        # Tags = @()

        # A URL to the license for this module.
        # LicenseUri = ''
    
        # A URL to the main website for this project.
        # ProjectUri = ''
        
        # A URL to an icon representing this module.
        # IconUri = ''
        
        # ReleaseNotes of this module
        # ReleaseNotes = ''
        
        # External dependent modules of this module
        # ExternalModuleDependencies = ''
    } # End of PSData hashtable
} # End of PrivateData hashtable
```
***注:*** DscResourcesToExport は、最新の PowerShell バージョン 5.0 でのみサポートされています。 以前の PowerShell バージョンで実行している場合は、フィールドを更新できません。


<!--HONumber=Oct16_HO1-->


