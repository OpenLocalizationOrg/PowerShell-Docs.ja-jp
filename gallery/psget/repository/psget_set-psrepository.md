# Set-PSRepository

PSRepository セットは、登録されているリポジトリの値を設定します。

## 説明

セット PSRepository コマンドレットは、登録されているモジュールのリポジトリの値を設定します。

## コマンドレット構文

```powershell
Get-Command -Name Set-PSRepository -Module PowerShellGet -Syntax
```
## コマンドレットのオンライン ヘルプ リファレンス

[セット PSRepository](http://go.microsoft.com/fwlink/?LinkID=517128)

## コマンド例

```powershell
PS C:\> Register-PSRepository -Name myRepository -SourceLocation "https://www.myget.org/F/powershellgetdemo/api/v2" -InstallationPolicy Trusted
PS C:\> Get-PSRepository
Name                      InstallationPolicy   SourceLocation
----                      ------------------   --------------
myRepository              Trusted              https://www.myget.org/F/powershellgetdemo/api/v2

# Set installation Policy for a repository
PS C:\> Set-PSRepository -Name myRepository -InstallationPolicy Untrusted
PS C:\> Get-PSRepository

Name                      InstallationPolicy   SourceLocation
----                      ------------------   --------------
myRepository              Untrusted            https://www.myget.org/F/powershellgetdemo/api/v2
```


### 共有のサポート スクリプト セット PSRepository コマンドレット

セット PSRepository コマンドレットを使用して、追加、 **ScriptSourceLocation** と **ScriptPublishLocation** 、PSRepository にします。
```powershell
# Add script sharing locations to an existing PSRepository using Set-PSRepository object.
Set-PSRepository -Name MyGallery `
                 -ScriptSourceLocation https://MyGallery.com/api/v2/items/psscript/ `
                 -ScriptPublishLocation https://MyGallery.com/api/v2/package/

Get-PSRepository -Name GalleryINT | Format-List * -Force

Name : GalleryINT
SourceLocation : https://MyGallery.com/api/v2/
Trusted : True
Registered : True
InstallationPolicy : Trusted
PackageManagementProvider : NuGet
PublishLocation : https://MyGallery.com/api/v2/package/
ScriptSourceLocation : https://MyGallery.com/api/v2/items/psscript/
ScriptPublishLocation : https://MyGallery.com/api/v2/package/
ProviderOptions : {}

```


<!--HONumber=Oct16_HO1-->


