# Save-Script

Save-Script コマンドレットでは、指定した場所に保存することによって、スクリプト ファイルを確認できます。

## 説明

保存スクリプト コマンドレットは、指定されたスクリプトを保存します。

## コマンドレット構文

```powershell
Get-Command -Name Save-Script -Module PowerShellGet -Syntax
```
## コマンドレットのオンライン ヘルプ リファレンス

[スクリプトの保存](http://go.microsoft.com/fwlink/?LinkId=619786)

## コマンド例

### 例 1: リポジトリからスクリプトを保存します。
このコマンドは、スクリプトをローカル フォルダー C:\ScriptSharingDemo GalleryINT リポジトリから Fabrikam ClientScript の最新バージョンを保存します。

```powershell
Save-Script -Name Fabrikam-ClientScript -Repository GalleryINT -Path C:\ScriptSharingDemo
```

### 例 2: 検索スクリプトのコマンドレットからにパイプして、スクリプトのバージョンを保存します。

最初のコマンドで GalleryINT リポジトリから Fabrikam ClientScript のバージョン 1.5 を検索し、C:\ScriptSharingDemo フォルダーに保存します。

2 番目のコマンドでは、保存したスクリプトのメタデータを検証します。

```powershell
Find-Script -Name Fabrikam-ClientScript -Repository GalleryINT -RequiredVersion 1.5 | Save-Script -Path C:\\ScriptSharingDemo
Test-ScriptFileInfo C:\\ScriptSharingDemo\\Fabrikam-ClientScript.ps1

Version Name Author Description
------- ---- ------ -----------
1.5 Fabrikam-ClientScript manikb Description for the Fabrikam-ClientScript script
```


<!--HONumber=Oct16_HO1-->


