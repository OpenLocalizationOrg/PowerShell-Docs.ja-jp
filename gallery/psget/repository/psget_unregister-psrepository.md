# Unregister-PSRepository

リポジトリの登録を解除します。

## 説明

登録解除 PSRepository コマンドレットは、現在のユーザーを格納するリポジトリを登録解除します。
- 登録を解除し、PSGallery リポジトリの再登録は、企業に対して許可およびシナリオが切断されます。
- ユーザーが実行するだけで、PSGallery を再登録できます。 `Register-PSRepository -Default`
- PSGallery 既定値は、モジュールの発行と発行スクリプトのコマンドレットでリポジトリを発行するため、PSGallery が登録されているリポジトリのリストに使用できない場合、エラーがスローされます。

## コマンドレット構文

```powershell
Get-Command -Name Unregister-PSRepository -Module PowerShellGet -Syntax
```
## コマンドレットのオンライン ヘルプ リファレンス

[登録を解除 PSRepository](http://go.microsoft.com/fwlink/?LinkID=517130)

## コマンド例

```powershell
Unregister-PSRepository -Name "MyPrivateGallery"

Get-PSRepository exp | Unregister-PSRepository
```

### 登録を解除し、PSGallery リポジトリの再登録は、企業に対して許可およびシナリオが切断されます。
```powershell

# Unregister PSGallery repository
Unregister-PSRepository PSGallery

# Publish-Module throws an error when PSGallery is not a registered repository
Publish-Module -Name MyModule
publish-module : Unable to find repository 'PSGallery'. Use Get-PSRepository to see all available repositories. Try again after specifying a valid repository name. You can use 'Register-PSRepository -Default' to register the PSGallery repository.
At line:1 char:1
+ publish-module -name mymodule
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (PSGallery:String) [Publish-Module], ArgumentException
    + FullyQualifiedErrorId : PSGalleryNotFound,Publish-Module

# Re-register PSGallery repository
Register-PSRepository -Default
```

<!--HONumber=Oct16_HO1-->


