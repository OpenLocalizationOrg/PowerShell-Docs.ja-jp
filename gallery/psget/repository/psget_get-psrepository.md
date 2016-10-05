# Get-PSRepository

コンピューターに登録されているリポジトリを取得します。

## 説明

Get PSRepository コマンドレットでは、コンピューター上の現在のユーザーに対して登録されている PowerShell モジュールのリポジトリを取得します。

登録済みのリポジトリごとには、Get PSRepository は、登録されたリポジトリの登録を解除するため、登録解除 PSRepository を必要に応じてパイプすることもできる PSRepository オブジェクトを返します。

## コマンドレット構文
```powershell
Get-Command -Name Get-PSRepository -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[Get PSRepository](http://go.microsoft.com/fwlink/?LinkID=517127)

## コマンド例

```powershell

# Properties of Get-PSRepository returned object
Get-PSRepository PSGallery | Format-List * -Force

Name                      : PSGallery
SourceLocation            : https://www.powershellgallery.com/api/v2/
Trusted                   : False
Registered                : True
InstallationPolicy        : Untrusted
PackageManagementProvider : NuGet
PublishLocation           : https://www.powershellgallery.com/api/v2/package/
ScriptSourceLocation      : https://www.powershellgallery.com/api/v2/items/psscript/
ScriptPublishLocation     : https://www.powershellgallery.com/api/v2/package/
ProviderOptions           : {}

# Get all registered repositories
Get-PSRepository

# Get a specific registered repository
Get-PSRepository PSGallery

Name                      InstallationPolicy   SourceLocation
----                      ------------------   --------------
PSGallery                 Untrusted            https://www.powershellgallery.com/api/v2/

# Get registered repository with wildcards
Get-PSRepository *Gallery*

```

<!--HONumber=Oct16_HO1-->


