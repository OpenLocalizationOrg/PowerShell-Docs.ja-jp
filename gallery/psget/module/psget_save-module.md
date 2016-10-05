# Save-Module

これをインストールすることがなくモジュールをローカルに保存します。

## 説明

保存モジュール コマンドレットは、検査のため、指定されたリポジトリからローカルでモジュールを保存します。 モジュールがインストールされていません。

## コマンドレット構文
```powershell
Get-Command -Name Save-Module -Module PowerShellGet -Syntax
```

## コマンドレットのオンライン ヘルプ リファレンス

[保存モジュール](http://go.microsoft.com/fwlink/?LinkId=531351)

## コマンド例

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


# Find a command and save its module
# This command finds the specified command, and then passes it to Save-Module to save it to the C:\temp folder.
Find-Command -Name "Get-NestedRequiredModule4" -Repository "INT" | Save-Module -Path "C:\temp\" -Verbose


# Save the role capability modules by piping the Find-RoleCapability output to Save-Module cmdlet.
Find-RoleCapability -Name Maintenance,MyJeaRole | Save-Module -Path C:\MyModulesPath

```


<!--HONumber=Oct16_HO1-->


