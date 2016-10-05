PowerShell ギャラリーの状態
=========================


## 8/10/2016 - 解決: 電子メールを送信できません。 cgadmin@microsoft.com

__影響の概要__: 8/5/2016 と 8/10/2016 では、顧客にでした。 電子メールを送信 cgadmin@microsoft.com, または問い合わせ機能を使用します。  
__根本的な原因__: エンジニアは、電子メール アカウントの構成の変更に原因があることを突き止めました。  
__解決策__: エンジニアは、構成の問題を解決するための作業を行いました。  
__次のステップ__: 問い合わせ] リンクを使用するかにメールを送信したかどうか cgadmin@microsoft.com には時間、私たちが応答していない、もう一度やり直してください。 ご不便をおかけして申し訳ございませんでした。



## 2016 年 7 月 13 日 - アイテムのダウンロードに失敗しました

__影響の概要__: 2016 年 7 月 11 日から 2016 年 7 月 13 日までの期間中に一部のお客様で、PowerShell ギャラリーからアイテムをダウンロードする際に問題が発生しました。 この問題は、Install-Module/Install-Script および Save-Module/Save-Script から返された次のエラー メッセージに示されていたようです。

```PowerShell
PS C:\> Install-Module xStorage 
PackageManagement\Install-Package : Package 'xStorage' failed to be installed because: 
End of Central Directory record could not be found. At C:\Program 
Files\WindowsPowerShell\Modules\PowerShellGet\1.0.0.1\PSModule.psm1:1375 char:21 + ... 
$null = PackageManagement\Install-Package @PSBoundParameters + 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ + CategoryInfo : InvalidResult: 
(xStorage:String) [Install-Package], Exception + FullyQualifiedErrorId : Package '{0}' 
failed to be installed because: {1},Microsoft.PowerShell.PackageManagement.Cmdlets.InstallPackage 
```

__暫定的な根本原因__: エンジニアは、2016 年 7 月 11 日に PowerShell ギャラリーに展開した Azure コンテンツ配信ネットワーク (CDN) に関係する問題であることを突き止めました。  
__対応策__: エンジニアは、PowerShell ギャラリーで Azure CDN を無効にしました。  
__次の手順__: 根本原因を調査し、今後の問題発生を防止するためのソリューションを開発します。


## 2016 年 5 月 19 日 - アイテムのダウンロードに失敗しました
__影響の概要__: 2016 年 5 月 17 日から 2016 年 5 月 19 日までの期間中に一部のお客様で、PowerShell ギャラリーからアイテムをダウンロードする際に問題が発生しました。 この問題は、Install-Module/Install-Script および Save-Module/Save-Script から返された次のエラー メッセージに示されていたようです。

```PowerShell
VERBOSE: Hash for package 'AzureRM.OperationalInsights' does not match hash provided from the server.
VERBOSE: InstallPackageLocal' - name='AzureRM.OperationalInsights', version='1.0.8',
destination='C:\Users\jbritt\AppData\Local\Temp\2\1741355729'
WARNING: Package 'AzureRM.OperationalInsights' failed to be installed because: 
End of Central Directory record could not be found. 
WARNING: Dependent Package 'AzureRM.OperationalInsights' failed to install. 
WARNING: Package 'AzureRM' failed to install. 
VERBOSE: Module 'AzureRM.Network' was saved successfully. 
VERBOSE: Saving the dependency module 'AzureRM.NotificationHubs' with version '1.0.8' for the 
module 'AzureRM'. 
VERBOSE: Module 'AzureRM.NotificationHubs' was saved successfully. 
PackageManagement\Save-Package : Unable to save the module 'AzureRM'. At C:\Program 
Files\WindowsPowerShell\Modules\PowerShellGet\1.0.0.1\PSModule.psm1:1187 char:21 + 
$null = PackageManagement\Save-Package @PSBoundParameters + 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ + 
CategoryInfo : InvalidOperation: (Microsoft.Power...ets.SavePackage:SavePackage) 
[Save-Package], Exception + FullyQualifiedErrorId : ProviderFailToDownloadFile,
Microsoft.PowerShell.PackageManagement.Cmdlets.SavePackage 
```

__暫定的な根本原因__: エンジニアは、2016 年 5 月 17 日に PowerShell ギャラリーに展開した Azure コンテンツ配信ネットワーク (CDN) の基になるプロバイダーで障害が発生したことを突き止めました。  
__対応策__: エンジニアは、PowerShell ギャラリーで Azure CDN を無効にしました。  
__次の手順__: 根本原因を調査し、今後の問題発生を防止するためのソリューションを開発します。


<!--HONumber=Oct16_HO1-->


