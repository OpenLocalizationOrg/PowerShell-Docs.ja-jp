---
title: "DSC リソースのデバッグ"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 461cd535bfbdda6cc90364aace9912f713431c21

---

# DSC リソースのデバッグ

> 適用先: Windows PowerShell 5.0

PowerShell 5.0 では、構成が適用されているときに DSC リソースをデバッグできる新機能が Desired State Configuraiton (DSC) に導入されました。

## DSC デバッグの有効化
リソースをデバッグする前に、[Enable-DscDebug](https://technet.microsoft.com/en-us/library/mt517870.aspx) コマンドレットを呼び出すことによって、デバッグを有効にする必要があります。 このコマンドレットは、必須パラメーター **BreakAll** を取ります。 

[Get-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx) への呼び出しの結果を参照して、デバッグが有効になっていることを確認できます。 次の PowerShell 出力は、デバッグを有効にした結果を示しています。


```powershell
PS C:\DebugTest> $LCM = Get-DscLocalConfigurationManager

PS C:\DebugTest> $LCM.DebugMode
NONE

PS C:\DebugTest> Enable-DscDebug -BreakAll

PS C:\DebugTest> $LCM = Get-DscLocalConfigurationManager

PS C:\DebugTest> $LCM.DebugMode
ForceModuleImport
ResourceScriptBreakAll

PS C:\DebugTest>
```


## デバッグを有効にした構成の開始
DSC リソースをデバッグするには、そのリソースを呼び出す構成を開始します。 この例では、"WindowsPowerShellWebAccess" 機能がインストールされていることを確認するために [WindowsFeature](windowsfeatureResource.md) リソースを呼び出す単純な構成を見ていきます。

```powershell
Configuration PSWebAccess
    {
    Import-DscResource -ModuleName 'PsDesiredStateConfiguration'
    Node localhost
        {
        WindowsFeature PSWA
            {
            Name = 'WindowsPowerShellWebAccess'
            Ensure = 'Present'
            }
        }
    }
PSWebAccess
```
構成をコンパイルした後、[Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) を呼び出して開始します。 構成は、ローカル構成マネージャー (LCM) が構成の最初のリソースを呼び出したときに停止します。 `-Verbose` および `-Wait` パラメーターを使用した場合、デバッグを開始するために入力する必要がある行が出力に表示されます。

```powershell
PS C:\DebugTest> Start-DscConfiguration .\PSWebAccess -Wait -Verbose
VERBOSE: Perform operation 'Invoke CimMethod' with following parameters, ''methodName' = SendConfigurationApply,'className' = MSFT_DSCLocalConfiguration
Manager,'namespaceName' = root/Microsoft/Windows/DesiredStateConfiguration'.
VERBOSE: An LCM method call arrived from computer TEST-SRV with user sid S-1-5-21-2127521184-1604012920-1887927527-108583.
VERBOSE: An LCM method call arrived from computer TEST-SRV with user sid S-1-5-21-2127521184-1604012920-1887927527-108583.
VERBOSE: [TEST-SRV]: LCM:  [ Start  Set      ]
WARNING: [TEST-SRV]:                            [DSCEngine] Warning LCM is in Debug 'ResourceScriptBreakAll' mode.  Resource script processing will 
be stopped to wait for PowerShell script debugger to attach.
VERBOSE: [TEST-SRV]:                            [DSCEngine] Importing the module C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateCo
nfiguration\DscResources\MSFT_RoleResource\MSFT_RoleResource.psm1 in force mode.
VERBOSE: [TEST-SRV]: LCM:  [ Start  Resource ]  [[WindowsFeature]PSWA]
VERBOSE: [TEST-SRV]: LCM:  [ Start  Test     ]  [[WindowsFeature]PSWA]
VERBOSE: [TEST-SRV]:                            [[WindowsFeature]PSWA] Importing the module MSFT_RoleResource in force mode.
WARNING: [TEST-SRV]:                            [[WindowsFeature]PSWA] Resource is waiting for PowerShell script debugger to attach.  Use the follow
ing commands to begin debugging this resource script:
Enter-PSSession -ComputerName TEST-SRV -Credential <credentials>
Enter-PSHostProcess -Id 9000 -AppDomainName DscPsPluginWkr_AppDomain
Debug-Runspace -Id 9
```
この時点で、LCM はリソースを呼び出し、最初のブレーク ポイントに到達しています。 出力の最後の 3 行は、プロセスに接続し、リソース スクリプトのデバッグを開始する方法を示しています。

## リソース スクリプトのデバッグ

PowerShell ISE の新しいインスタンスを開始します。 コンソール ウィンドウで、`Start-DscConfiguration` 出力から出力の最後の 3 行をコマンドとして入力し、`<credentials>` を有効なユーザー資格情報に置き換えます。 次のようなプロンプトが表示されます。

```powershell
[TEST-SRV]: [DBG]: [Process:9000]: [RemoteHost]: PS C:\DebugTest>>
```

スクリプト ウィンドウが開いてリソース スクリプトが表示され、**Test-TargetResource** 関数の最初の行 (クラスベースのリソースの **Test()** メソッド) でデバッガーが停止します。
ISE でデバッグ コマンドを使うと、リソース スクリプトをステップ実行したり、変数の値を確認したり、呼び出し履歴を表示したりできます。 PowerShell ISE でのデバッグについての詳細は、「[Windows PowerShell ISE でスクリプトをデバッグする方法](https://technet.microsoft.com/en-us/library/dd819480.aspx)」を参照してください。 リソース スクリプト (またはクラス) のすべての行がブレークポイントとして設定されていることに注意してください。

## DSC デバッグの無効化

[Enable-DscDebug](https://technet.microsoft.com/en-us/library/mt517870.aspx)を呼び出した後では、[Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) を呼び出すたびに構成でデバッガー中断が行われるようになります。 構成を通常どおりに実行できるようにするには、[Disable-DscDebug](https://technet.microsoft.com/en-us/library/mt517872.aspx) コマンドレットを呼び出してデバッグを無効化する必要があります。

>**注:** 再起動をしても、LCM のデバッグ状態は変更されません。 デバッグが有効になっている場合、再起動後に構成を開始してもデバッグ中断が行われます。


## 参照
- [MOF を使用したカスタム DSC リソースの記述](authoringResourceMOF.md) 
- [PowerShell クラスを使用したカスタム DSC リソースの記述](authoringResourceClass.md)




<!--HONumber=Oct16_HO1-->


