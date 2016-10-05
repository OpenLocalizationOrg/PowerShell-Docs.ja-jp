## "警告: パッケージ 'パッケージ名' をダウンロードできませんでした" 問題を解決する方法




Install-Module または Update-Module が一部のコンピューターで時折失敗することが報告されています。
調査の結果、ネットワーク接続の問題であることが判明しました。
最近、パッケージのダウンロードが安定するように、NuGet プロバイダーを更新しました。
下の指示に従い、最新版の NuGet プロバイダーをインストールし、モジュールをインストールするか、更新できます。
以下、'Azure' モジュールをサンプルとして使用します。

```powershell
Install-PackageProvider NuGet -MinimumVersion 2.8.5.206 -Force
Launch new PowerShell Console
Update-Module Azure -Verbose
```


<!--HONumber=Oct16_HO1-->


