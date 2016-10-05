# Update-Module

ダウンロードし、[オンライン ギャラリーからの最新バージョンの指定されたモジュールをローカル コンピューターにインストールします。

## 説明

更新プログラム モジュール コマンドレットは、ローカル コンピューターにインストール モジュールを実行して、オンライン ギャラリーからインストールされた Windows PowerShell モジュールの新しいバージョンをインストールします。

既定では、オンライン ギャラリーで指定されたモジュールの最新バージョンがインストールされている、必要なバージョンを指定しない限りです。 モジュールの名前を指定して、インストールされている既存のモジュールを更新することができます。更新モジュール検索 $env:path: psmodulepath 環境変数を更新するモジュールです。

名前のパラメーターを指定しない更新モジュールを実行しているローカル コンピューターの更新可能なすべてのモジュールを更新します。

### メモ

- このコマンドレットは、Windows PowerShell 3.0 または Windows 7 または Windows 2008 R2 および Windows の今後のリリースでの Windows PowerShell の今後のリリースを実行します。
- Name パラメーターで指定したモジュールは、インストール モジュールを使用してインストールされていない、エラーが発生します。 更新プログラム モジュールは、オンライン ギャラリーから、インストール モジュールを実行して、インストールされているモジュールでのみ実行できます。
- 更新モジュールが使用されているバイナリを更新しようとすると、更新モジュールは、問題のプロセスを識別し、プロセスを停止した後、更新モジュールを再試行することを知らせるエラーを返します。
- PowerShell 5.0 または新しいバージョンでは、更新モジュールは、モジュールを更新するときにバージョンが追加、最新 (または指定された)、モジュールの新旧のバージョンがサイド バイ サイド同じディレクトリにします。 そのように言ってし、これらのコマンドの出力の例を表示するために便利になります。


## コマンドレット構文
```powershell
Get-Command -Name Update-Module -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[更新プログラム モジュール](http://go.microsoft.com/fwlink/?LinkID=398576)


## コマンド例

```powershell
PS C:\\windows\\system32> Update-Module -Name ContosoServer -RequiredVersion 1.5
PS C:\\windows\\system32> Get-Module -ListAvailable -Name ContosoServer | Format-List Name,Version,ModuleBase
Name : ContosoServer
Version : 2.0
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\2.0
Name : ContosoServer
Version : 1.5
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\1.5
Name : ContosoServer
Version : 1.0
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\1.0
PS C:\\windows\\system32> Get-InstalledModule
Version Name Repository Description
------- ---- ---------- -----------
1.0 ContosoServer MSPSGallery ContosoServer module
1.5 ContosoServer MSPSGallery ContosoServer module
2.0 ContosoServer MSPSGallery ContosoServer module
PS C:\\windows\\system32> Update-Module -Name ContosoServer
PS C:\\windows\\system32> Get-Module -ListAvailable -Name ContosoServer | Format-List Name,Version,ModuleBase
Name : ContosoServer
Version : 2.8.1
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\2.8.1
Name : ContosoServer
Version : 2.0
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\2.0
Name : ContosoServer
Version : 1.5
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\1.5
Name : ContosoServer
Version : 1.0
ModuleBase : C:\\Program Files\\WindowsPowerShell\\Modules\\ContosoServer\\1.0
PS C:\\windows\\system32> Get-InstalledModule
Version Name Repository Description
------- ---- ---------- -----------
1.0 ContosoServer MSPSGallery ContosoServer module
1.5 ContosoServer MSPSGallery ContosoServer module
2.0 ContosoServer MSPSGallery ContosoServer module
2.8.1 ContosoServer MSPSGallery ContosoServer module
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

<!--HONumber=Oct16_HO1-->


