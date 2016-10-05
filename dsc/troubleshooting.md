---
title: "DSC のトラブルシューティング"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4830be14b105485c50446f06e9d36491b4c4fe44

---

# DSC のトラブルシューティング

>適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

このトピックでは、問題が発生した場合の DSC のトラブルシューティング方法について説明します。

## Get-DscConfigurationStatus の使用

[Get-DscConfigurationStatus](https://technet.microsoft.com/en-us/library/mt517868.aspx) コマンドレットは、ターゲット ノードから構成状態に関する情報を取得します。 構成の実行が成功したかどうかについての基本情報を含む、リッチ オブジェクトが返されます。 オブジェクトを調べ、次に挙げるような構成の実行に関する詳細を知ることができます:

* 失敗したすべてのリソース
* 再起動を要求したすべてのリソース
* 構成の実行時のメタ構成の設定
* など

次のパラメーター セットは、最後の構成の実行状況に関する情報を返します。

```powershell
Get-DscConfigurationStatus  [-CimSession <CimSession[]>] 
                            [-ThrottleLimit <int>] 
                            [-AsJob] 
                            [<CommonParameters>]
```
次のパラメーター セットは、以前のすべての構成の実行状況に関する情報を返します。

```powershell
Get-DscConfigurationStatus  -All 
                            [-CimSession <CimSession[]>] 
                            [-ThrottleLimit <int>] 
                            [-AsJob] 
                            [<CommonParameters>]
```

## 例

```powershell
PS C:\> $Status = Get-DscConfigurationStatus 

PS C:\> $Status

Status      StartDate               Type            Mode    RebootRequested     NumberOfResources
------      ---------               ----            ----    ---------------     -----------------
Failure     11/24/2015  3:44:56     Consistency     Push    True                36

PS C:\> $Status.ResourcesNotInDesiredState

ConfigurationName       :   MyService
DependsOn               :   
ModuleName              :   PSDesiredStateConfiguration
ModuleVersion           :   1.1
PsDscRunAsCredential    :   
ResourceID              :   [File]ServiceDll
SourceInfo              :   c:\git\CustomerService\Configs\MyCustomService.ps1::5::34::File
DurationInSeconds       :   0.19
Error                   :   SourcePath must be accessible for current configuration. The related file/directory is:
                            \\Server93\Shared\contosoApp.dll. The related ResourceID is [File]ServiceDll
FinalState              :   
InDesiredState          :   False
InitialState            :   
InstanceName            :   ServiceDll
RebootRequested         :   False
ReosurceName            :   File
StartDate               :   11/24/2015  3:44:56
PSComputerName          :
```

## スクリプトが実行されない: DSC ログを使用したスクリプト エラーの診断

すべての Windows ソフトウェアと同じく、DSC は[イベント ビューアー](http://windows.microsoft.com/windows/what-information-event-logs-event-viewer)から参照可能な[ログ](https://msdn.microsoft.com/library/windows/desktop/aa363632.aspx)にエラーとイベントを記録します。 これらのログを調べることは、特定の操作が失敗した理由や、今後エラーを防止する方法を理解するために役立ちます。 構成スクリプトの記述は複雑になることがあります。そのため、作成しながらエラーをより簡単に追跡できるように、DSC ログ リソースを使用して DSC 分析イベント ログで構成の進行状況を追跡してください。

## DSC イベント ログの場所

イベント ビューアーでは、DSC イベントは **Applications and Services Logs/Microsoft/Windows/Desired State Configuration** にあります。

対応する PowerShell コマンドレット [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) を次のように実行して、イベント ログを表示することもできます。

```
PS C:\> Get-WinEvent -LogName "Microsoft-Windows-Dsc/Operational"
   ProviderName: Microsoft-Windows-DSC
TimeCreated                     Id LevelDisplayName Message                                                                                                  
-----------                     -- ---------------- -------                                                                                                  
11/17/2014 10:27:23 PM        4102 Information      Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
```

上に示すように、DSC のプライマリ ログ名は **Microsoft->Windows->DSC** です (簡略化のため、Windows の下にあるその他のログ名は表示していません)。 完全なログ名を作成するには、プライマリ名にチャネル名を追加します。 DSC エンジンは、主に 3 種類のログに書き込みます。[操作ログ、分析ログ、およびデバッグ ログ](https://technet.microsoft.com/library/cc722404.aspx)です。 分析ログとデバッグ ログは既定でオフになっているため、それらをイベント ビューアーで有効にする必要があります。 これを行うには、Windows PowerShell で「Show-EventLog」と入力するか、または、**[スタート]** ボタンをクリックし、**[コントロール パネル]**、**[管理ツール]**、**[イベント ビューアー]** の順にクリックして、イベント ビューアーを開きます。 イベント ビューアーの **[表示]** メニューで、**[分析およびデバッグ ログの表示]** をクリックします。 分析チャネルのログ名は **Microsoft-Windows-Dsc/Analytic** で、デバッグ チャネルのログ名は **Microsoft-Windows-Dsc/Debug** です。 次の例に示すように、[wevtutil](https://technet.microsoft.com/library/cc732848.aspx) ユーティリティを使用してログを有効にすることもできます。

```powershell
wevtutil.exe set-log “Microsoft-Windows-Dsc/Analytic” /q:true /e:true
```

## DSC ログに含まれる内容

DSC ログは、メッセージの重要度に基づいて 3 つのログ チャネルに分割されています。 DSC の操作ログにはすべてのエラー メッセージが含まれ、問題の識別に使用できます。 分析ログには、より大量のイベントが含まれ、エラーがどこで発生したかを識別できます。 このチャネルには、詳細メッセージも含まれます (ある場合)。 デバッグ ログには、エラーがどのように発生したかを理解するのに役立つログが含まれています。 DSC イベント メッセージは、すべてのイベント メッセージの先頭が、DSC 操作を一意に表すジョブ ID になるように構成されています。 次の例では、DSC 操作ログに記録された最初のイベントからメッセージを取得しようとしています。

```powershell
PS C:\> $AllDscOpEvents = Get-WinEvent -LogName "Microsoft-Windows-Dsc/Operational"
PS C:\> $FirstOperationalEvent = $AllDscOpEvents[0]
PS C:\> $FirstOperationalEvent.Message
Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
Consistency engine was run successfully. 
```

DSC イベントは、ユーザーが 1 つの DSC ジョブからイベントを集計できるように特定の構造に記録されます。 その構造は次のとおりです。

**ジョブ ID: <Guid>**
**<Event Message>**

## 1 つの DSC 操作からのイベントの収集

DSC イベント ログには、各種の DSC 操作によって生成されたイベントが含まれています。 ただし、通常、関心があるのはある特定の操作の詳細のみです。 すべての DSC ログは、それぞれの DSC 操作に対して一意であるジョブ ID プロパティによってグループ化できます。 ジョブ ID は、すべての DSC イベントの最初のプロパティ値として表示されます。 次の手順では、グループ化された配列構造にすべてのイベントを蓄積する方法について説明します。

```powershell
<##########################################################################
 Step 1 : Enable analytic and debug DSC channels (Operational channel is enabled by default)
###########################################################################>
 
wevtutil.exe set-log “Microsoft-Windows-Dsc/Analytic” /q:true /e:true
wevtutil.exe set-log “Microsoft-Windows-Dsc/Debug” /q:True /e:true
 
<##########################################################################
 Step 2 : Perform the required DSC operation (Below is an example, you could run any DSC operation instead)
###########################################################################>
 
Get-DscLocalConfigurationManager
 
<##########################################################################
Step 3 : Collect all DSC Logs, from the Analytic, Debug and Operational channels
###########################################################################>
 
$DscEvents=[System.Array](Get-WinEvent "Microsoft-Windows-Dsc/Operational") `
         + [System.Array](Get-WinEvent "Microsoft-Windows-Dsc/Analytic" -Oldest) `
         + [System.Array](Get-WinEvent "Microsoft-Windows-Dsc/Debug" -Oldest)
 
 
<##########################################################################
 Step 4 : Group all logs based on the job ID
###########################################################################>
$SeparateDscOperations = $DscEvents | Group {$_.Properties[0].value}  
```

ここでは、変数 `$SeparateDscOperations` にジョブ ID でグループ化されたログが含まれています。 この変数の各配列要素は、さまざまな DSC 操作によって記録されたイベントのグループを表しており、ログの詳細にアクセスできるようになっています。

```
PS C:\> $SeparateDscOperations
 
Count Name                      Group                                                                     
----- ----                      -----                                                                     
   48 {1A776B6A-5BAC-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord, System.Diagnostics....
   40 {E557E999-5BA8-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord, System.Diagnostics....
PS C:\> $SeparateDscOperations[0].Group
   ProviderName: Microsoft-Windows-DSC
TimeCreated                     Id LevelDisplayName Message                                               
-----------                     -- ---------------- -------                                               
12/2/2013 3:47:29 PM          4115 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4198 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4114 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4102 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4098 Warning          Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4098 Warning          Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4176 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...      
12/2/2013 3:47:29 PM          4182 Information      Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : ...       
```

[Where-Object](https://technet.microsoft.com/library/ee177028.aspx) を使用して変数 `$SeparateDscOperations` 内のデータを抽出できます。 次に、DSC のトラブルシューティングに役立つデータを抽出する 5 つのシナリオを示します。

### 1: 操作エラー

すべてのイベントには[重大度レベル](https://msdn.microsoft.com/library/dd996917(v=vs.85))があります。 この情報を使用して、エラー イベントを識別できます。

```
PS C:\> $SeparateDscOperations | Where-Object {$_.Group.LevelDisplayName -contains "Error"}
Count Name                      Group                                                                     
----- ----                      -----                                                                     
   38 {5BCA8BE7-5BB6-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord, System.Diagnostics....
```

### 2: 過去 30 分間に実行された操作の詳細

それぞれの Windows イベントのプロパティである `TimeCreated` は、イベントが作成された時刻を示します。 このプロパティを特定の日付/時刻オブジェクトと比較すると、すべてのイベントをフィルター処理できます。

```powershell
PS C:\> $DateLatest = (Get-Date).AddMinutes(-30)
PS C:\> $SeparateDscOperations | Where-Object {$_.Group.TimeCreated -gt $DateLatest}
Count Name                      Group                                                                     
----- ----                      -----                                                                     
    1 {6CEC5B09-5BB0-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord}   
```

### 3: 最後の操作からのメッセージ

最後の操作は、配列グループ `$SeparateDscOperations` の最初のインデックスに格納されています。 グループのメッセージに対してインデックス 0 のクエリを実行すると、最後の操作に関するすべてのメッセージが返されます。

```powershelll
PS C:\> $SeparateDscOperations[0].Group.Message
Job {5BCA8BE7-5BB6-11E3-BF41-00155D553612} : 
Running consistency engine.
Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : 
Configuration is sent from computer NULL by user sid S-1-5-18.
Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : 
Displaying messages from built-in DSC resources:
 WMI channel 1 
 ResourceID:  
 Message : [INCH-VM]:                            [] Starting consistency engine.
Job {1A776B6A-5BAC-11E3-BF41-00155D553612} : 
Displaying messages from built-in DSC resources:
 WMI channel 1 
 ResourceID:  
 Message : [INCH-VM]:                            [] Consistency check completed. 
```

### 4: 最近失敗した操作について記録されたエラー メッセージ

`$SeparateDscOperations[0].Group` には、最後の操作に関する一連のイベントが含まれています。 レベル表示名に基づいてイベントをフィルター処理するには、`Where-Object` コマンドレットを実行します。 結果は `$myFailedEvent` 変数に格納され、これをより細かく分析してイベント メッセージを取得できます。

```powershell
PS C:\> $myFailedEvent = ($SeparateDscOperations[0].Group | Where-Object {$_.LevelDisplayName -eq "Error"})
 
PS C:\> $myFailedEvent.Message
Job {5BCA8BE7-5BB6-11E3-BF41-00155D553612} : 
DSC Engine Error : 
 Error Message Current configuration does not exist. Execute Start-DscConfiguration command with -Path pa
rameter to specify a configuration file and create a current configuration first. 
Error Code : 1 
```

### 5: 特定のジョブ ID 用に生成されたすべてのイベント

`$SeparateDscOperations` はグループの配列で、グループのそれぞれに固有のジョブ ID として名前が付けられています。 `Where-Object` コマンドレットを実行すると、特定のジョブ ID を持つイベントのグループを抽出できます。

```powershell
PS C:\> ($SeparateDscOperations | Where-Object {$_.Name -eq $jobX} ).Group

   ProviderName: Microsoft-Windows-DSC
 
TimeCreated                     Id LevelDisplayName Message                                               
-----------                     -- ---------------- -------                                               
12/2/2013 4:33:24 PM          4102 Information      Job {847A5619-5BB2-11E3-BF41-00155D553612} : ...      
12/2/2013 4:33:24 PM          4168 Information      Job {847A5619-5BB2-11E3-BF41-00155D553612} : ...      
12/2/2013 4:33:24 PM          4146 Information      Job {847A5619-5BB2-11E3-BF41-00155D553612} : ...      
12/2/2013 4:33:24 PM          4120 Information      Job {847A5619-5BB2-11E3-BF41-00155D553612} : ...  
```

## xDscDiagnostics を使用した DSC ログの分析

**xDscDiagnostics** は、コンピューター上の DSC 障害の分析に役立つ複数の関数で構成される PowerShell モジュールです。 これらの関数は、過去の DSC 操作からのすべてのローカル イベント、またはリモート コンピューター上の DSC イベントの識別に役立ちます (有効な資格情報を使用)。 ここでは、開始から終了まで 1 回の一意の DSC 実行を定義するために、DSC 操作という用語を使用します。 たとえば、`Test-DscConfiguration` は独立した DSC 操作です。 同様に、DSC の他のすべてのコマンドレット (`Get-DscConfiguration` や `Start-DscConfiguration` など) をそれぞれ別の DSC 操作として識別できます。 この関数は、[xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) で説明されています。 ヘルプを参照するには、`Get-Help <cmdlet name>` を実行します。

### DSC 操作の詳細の取得 

`Get-xDscOperation` 関数は、1 台以上のコンピューターで実行される DSC 操作の結果を検索し、それぞれの DSC 操作で生成されたイベントのコレクションが含まれているオブジェクトを返すことができます。 たとえば、次の出力では、3 つのコマンドが実行されました。 1 つ目のコマンドは成功し、他の 2 つのコマンドは失敗しました。 これらの結果が `Get-xDscOperation` の出力にまとめられています。

```powershell
PS C:\DiagnosticsTest> Get-xDscOperation

ComputerName   SequenceId TimeCreated           Result   JobID                                 AllEvents            
------------   ---------- -----------           ------   -----                                 ---------            
SRV1   1          6/23/2016 9:37:52 AM  Failure  9701aadf-395e-11e6-9165-00155d390509  {@{Message=; TimeC...
SRV1   2          6/23/2016 9:36:54 AM  Failure  7e8e2d6e-395c-11e6-9165-00155d390509  {@{Message=; TimeC...
SRV1   3          6/23/2016 9:36:54 AM  Success  af72c6aa-3960-11e6-9165-00155d390509  {@{Message=Operati...

```

また、`Newest` パラメーターを使用して最近の操作の結果のみを表示するように指定することもできます。

```powershell
PS C:\DiagnosticsTest> Get-xDscOperation -Newest 5
ComputerName   SequenceId TimeCreated           Result   JobID                                 AllEvents            
------------   ---------- -----------           ------   -----                                 ---------            
SRV1   1          6/23/2016 4:36:54 PM  Success                                        {@{Message=; TimeC...
SRV1   2          6/23/2016 4:36:54 PM  Success  5c06402b-399b-11e6-9165-00155d390509  {@{Message=Operati...
SRV1   3          6/23/2016 4:36:54 PM  Success                                        {@{Message=; TimeC...
SRV1   4          6/23/2016 4:36:54 PM  Success  5c06402a-399b-11e6-9165-00155d390509  {@{Message=Operati...
SRV1   5          6/23/2016 4:36:51 PM  Success                                        {@{Message=; TimeC...
```

### DSC イベントの詳細の取得

`Trace-xDscOperation1 cmdlet returns an object containing a collection of events, their event types, and the message output generated from a particular DSC operation. Typically, when you find a failure in any of the operations using `Get-xdscoperation` では、その操作をトレースすると、どのイベントによってエラーが発生したかがわかります。

`SequenceID` パラメーターを使用して特定のコンピューターの特定の操作に対するイベントを取得します。 たとえば、`SequenceID` に 9 を指定すると、`Trace-xDscOperaion` は最後の操作から 9 番目の DSC 操作のトレースを取得します。

```powershell
PS C:\DiagnosticsTest> Trace-xDscOperation -SequenceID 9

ComputerName   EventType    TimeCreated           Message                                                                                             
------------   ---------    -----------           -------                                                                                             
SRV1   OPERATIONAL  6/24/2016 10:51:52 AM Operation Consistency Check or Pull started by user sid S-1-5-20 from computer NULL.                
SRV1   OPERATIONAL  6/24/2016 10:51:52 AM Running consistency engine.                                                                         
SRV1   OPERATIONAL  6/24/2016 10:51:52 AM The local configuration manager is updating the PSModulePath to WindowsPowerShell\Modules;C:\Prog...
SRV1   OPERATIONAL  6/24/2016 10:51:53 AM  Resource execution sequence :: [WindowsFeature]DSCServiceFeature, [xDSCWebService]PSDSCPullServer. 
SRV1   OPERATIONAL  6/24/2016 10:51:54 AM Consistency engine was run successfully.                                                            
SRV1   OPERATIONAL  6/24/2016 10:51:54 AM Job runs under the following LCM setting. ...                                                       
SRV1   OPERATIONAL  6/24/2016 10:51:54 AM Operation Consistency Check or Pull completed successfully. 
```

割り当てられた **GUID** を特定の DSC 操作に渡すと (`Get-xDscOperation` コマンドレットが返したときに)、その DSC 操作のイベントの詳細を取得できます。

```powershell
PS C:\DiagnosticsTest> Trace-xDscOperation -JobID 9e0bfb6b-3a3a-11e6-9165-00155d390509

ComputerName   EventType    TimeCreated           Message                                                                                             
------------   ---------    -----------           -------                                                                                             
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM Operation Consistency Check or Pull started by user sid S-1-5-20 from computer NULL.                
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCache.mof                             
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM Running consistency engine.                                                                         
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [] Starting consistency engine.                          
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Applying configuration from C:\Windows\System32\Configuration\Current.mof.                          
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Parsing the configuration to apply.                                                                 
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM  Resource execution sequence :: [WindowsFeature]DSCServiceFeature, [xDSCWebService]PSDSCPullServer. 
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ Start  Resource ]  [[WindowsFeature]DSCServiceFeature]                      
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Executing operations for PS DSC resource MSFT_RoleResource with resource name [WindowsFeature]DSC...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ Start  Test     ]  [[WindowsFeature]DSCServiceFeature]                      
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[WindowsFeature]DSCServiceFeature] The operation 'Get...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[WindowsFeature]DSCServiceFeature] The operation 'Get...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ End    Test     ]  [[WindowsFeature]DSCServiceFeature] True in 0.3130 sec...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ End    Resource ]  [[WindowsFeature]DSCServiceFeature]                      
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ Start  Resource ]  [[xDSCWebService]PSDSCPullServer]                        
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Executing operations for PS DSC resource MSFT_xDSCWebService with resource name [xDSCWebService]P...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ Start  Test     ]  [[xDSCWebService]PSDSCPullServer]                        
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[xDSCWebService]PSDSCPullServer] Check Ensure           
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[xDSCWebService]PSDSCPullServer] Check Port             
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[xDSCWebService]PSDSCPullServer] Check Physical Path ...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[xDSCWebService]PSDSCPullServer] Check State            
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [[xDSCWebService]PSDSCPullServer] Get Full Path for We...
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ End    Test     ]  [[xDSCWebService]PSDSCPullServer] True in 0.0160 seconds.
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]: LCM:  [ End    Resource ]  [[xDSCWebService]PSDSCPullServer]                        
SRV1   VERBOSE      6/24/2016 11:36:56 AM [SRV1]:                            [] Consistency check completed.                          
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCache.mof                             
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM Consistency engine was run successfully.                                                            
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM Job runs under the following LCM setting. ...                                                       
SRV1   OPERATIONAL  6/24/2016 11:36:56 AM Operation Consistency Check or Pull completed successfully.                                         
SRV1   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCache.mof
```

`Trace-xDscOperation` では、分析ログ、デバッグ ログ、および操作ログからイベントを集計するため、上記のようにこれらのログを有効にするように、プロンプトで要求されます。

また、`Trace-xDscOperation` の出力を変数に保存して、イベントに関する情報を収集することもできます。 次のコマンドを使用すると、特定の DSC 操作のすべてのイベントを表示できます。

```powershell
PS C:\DiagnosticsTest> $Trace = Trace-xDscOperation -SequenceID 4

PS C:\DiagnosticsTest> $Trace.Event
```

次の出力のように、`Get-WinEvent` コマンドレットと同じ結果が表示されます。

```powershell
   ProviderName: Microsoft-Windows-DSC

TimeCreated                     Id LevelDisplayName Message                                                                                           
-----------                     -- ---------------- -------                                                                                           
6/23/2016 1:36:53 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 1:36:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 2:07:00 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 2:07:01 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 2:36:55 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 2:36:56 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 3:06:55 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 3:06:55 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 3:36:55 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 3:36:55 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 4:06:53 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 4:06:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 4:36:52 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 4:36:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 5:06:52 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 5:06:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 5:36:54 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 5:36:54 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 6:06:52 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 6:06:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 6:36:56 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 6:36:57 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 7:06:52 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 7:06:53 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 7:36:53 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.     
6/23/2016 7:36:54 AM          4343 Information      The DscTimer has successfully run LCM method PerformRequiredConfigurationChecks with flag 5.      
6/23/2016 8:06:54 AM          4312 Information      The DscTimer is running LCM method PerformRequiredConfigurationChecks with the flag set to 5.
```

まず、`Get-xDscOperation` を使用して、コンピューターでの DSC 構成実行のうち、最新のいくつかを一覧表示すると理想的です。 次に、`Trace-xDscOperation` でいずれかの単一の操作を (その SequenceID または JobID を使用して) 調べると、バックグラウンドで何が行われたかがわかります。

### リモート コンピューターのイベントの取得

`Trace-xDscOperation` コマンドレットの `ComputerName` パラメーターを使用して、リモート コンピューターのイベントの詳細を取得します。 これを行う前に、ファイアウォール ルールを作成してリモート コンピューターでのリモート管理を許可する必要があります。

```powershell
New-NetFirewallRule -Name "Service RemoteAdmin" -DisplayName "Remote" -Action Allow
```
これで、`Trace-xDscOperation` への呼び出しでそのコンピューターを指定できます。

```powershell
PS C:\DiagnosticsTest> Trace-xDscOperation -ComputerName SRV2 -Credential Get-Credential -SequenceID 5

ComputerName   EventType    TimeCreated           Message
------------   ---------    -----------           -------
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM Operation Consistency Check or Pull started by user sid S-1-5-20 f...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCach...
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM Running consistency engine.
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [] Starting consistency...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Applying configuration from C:\Windows\System32\Configuration\Curr...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Parsing the configuration to apply.
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM  Resource execution sequence :: [WindowsFeature]DSCServiceFeature,...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ Start  Resource ]  [[WindowsFeature]DSCSer...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Executing operations for PS DSC resource MSFT_RoleResource with re...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ Start  Test     ]  [[WindowsFeature]DSCSer...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[WindowsFeature]DSCSer...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[WindowsFeature]DSCSer...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ End    Test     ]  [[WindowsFeature]DSCSer...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ End    Resource ]  [[WindowsFeature]DSCSer...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ Start  Resource ]  [[xDSCWebService]PSDSCP...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Executing operations for PS DSC resource MSFT_xDSCWebService with ...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ Start  Test     ]  [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ End    Test     ]  [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]: LCM:  [ End    Resource ]  [[xDSCWebService]PSDSCP...
SRV2   VERBOSE      6/24/2016 11:36:56 AM [SRV2]:                            [] Consistency check co...
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCach...
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM Consistency engine was run successfully.
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM Job runs under the following LCM setting. ...
SRV2   OPERATIONAL  6/24/2016 11:36:56 AM Operation Consistency Check or Pull completed successfully.
SRV2   ANALYTIC     6/24/2016 11:36:56 AM Deleting file from C:\Windows\System32\Configuration\DSCEngineCach...
```

## リソースが更新されない: キャッシュをリセットする方法

DSC エンジンは、効率化のために、PowerShell モジュールとして実装されているリソースをキャッシュします。 ただし、リソースを作成すると同時にテストする場合には、このことによって問題が発生することがあります。これは、DSC では、プロセスが再開されるまで、キャッシュされたバージョンを読み込むためです。 新しいバージョンが読み込まれるようにする唯一の方法は、DSC エンジンをホストしているプロセスを明示的に強制終了することです。

同様に、カスタム リソースを追加および変更した後で `Start-DscConfiguration` を実行した場合、その変更はコンピューターを再起動しないと反映されません。 これは、DSC が WMI Provider Host Process (WmiPrvSE) で動作し、通常、WmiPrvSE のインスタンスは同時にいくつも動作しているためです。 コンピューターを再起動すると、ホスト プロセスが再起動し、キャッシュがクリアされます。

コンピューターを再起動することなく、構成を正常にリサイクルし、キャッシュをクリアするには、ホスト プロセスを停止してから再起動する必要があります。 このことは、プロセスを識別し、停止し、再起動することで、インスタンスごとに実行できます。 また、以下に示すように、`DebugMode` を使用して PowerShell DSC リソースを再読み込みすることもできます。

DSC エンジンをホストしているプロセスを識別し、インスタンスごとにそのプロセスを停止するには、DSC エンジンをホストしている WmiPrvSE のプロセス ID を一覧表示します。 次に、プロバイダーを更新するために、次のコマンドを使用して WmiPrvSE プロセスを停止し、再度 **Start-DscConfiguration** を実行します。

```powershell
###
### find the process that is hosting the DSC engine
###
$dscProcessID = Get-WmiObject msft_providers | 
Where-Object {$_.provider -like 'dsccore'} | 
Select-Object -ExpandProperty HostProcessIdentifier 

###
### Stop the process
###
Get-Process -Id $dscProcessID | Stop-Process
```

## DebugMode の使用

ホスト プロセスの再起動時に `DebugMode` を使用して常にキャッシュをクリアするように、DSC ローカル構成マネージャー (LCM) を構成できます。 **TRUE** に設定すると、エンジンは常に PowerShell DSC リソースを再読み込みします。 リソースの書き込みが完了した後、**FALSE** に設定し直して、モジュールをキャッシュするようにエンジンの動作を戻すことができます。

次は、`DebugMode` がどのようにキャッシュを自動的に更新できるかを示すデモです。 まず、既定の構成を見てみましょう。

```
PS C:\> Get-DscLocalConfigurationManager
 
 
AllowModuleOverwrite           : False
CertificateID                  : 
ConfigurationID                : 
ConfigurationMode              : ApplyAndMonitor
ConfigurationModeFrequencyMins : 30
Credential                     : 
DebugMode                      : False
DownloadManagerCustomData      : 
DownloadManagerName            : 
LocalConfigurationManagerState : Ready
RebootNodeIfNeeded             : False
RefreshFrequencyMins           : 15
RefreshMode                    : PUSH
PSComputerName                 :  
```

`DebugMode` が **FALSE** に設定されています。

`DebugMode` のデモを設定するには、次の PowerShell リソースを使用します。

```powershell
function Get-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        $onlyProperty
    )
    return @{onlyProperty = Get-Content -Path "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"}
}
function Set-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        $onlyProperty
    )
    "1" | Out-File -PSPath "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"
}
function Test-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        $onlyProperty
    )
    return $false
} 
```

ここで、上記のリソースを使用して `TestProviderDebugMode` という名前で構成を作成します。

```powershell
Configuration ConfigTestDebugMode
{
    Import-DscResource -Name TestProviderDebugMode
    Node localhost
    {
        TestProviderDebugMode test
        {
            onlyProperty = "blah"
        }
    }
}
ConfigTestDebugMode
```

ファイル "**$env:SystemDrive\OutputFromTestProviderDebugMode.txt**" の内容は **1** になっています。

ここで、次のスクリプトを使用してプロバイダー コードを更新します。

```powershell
$newResourceOutput = Get-Random -Minimum 5 -Maximum 30
$content = @"
function Get-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        `$onlyProperty
    )
    return @{onlyProperty = Get-Content -Path "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"}
}
function Set-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        `$onlyProperty
    )
    "$newResourceOutput" | Out-File -PSPath C:\OutputFromTestProviderDebugMode.txt
}
function Test-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        `$onlyProperty
    )
    return `$false
}
"@ | Out-File -FilePath "C:\Program Files\WindowsPowerShell\Modules\MyPowerShellModules\DSCResources\TestProviderDebugMode\TestProviderDebugMode.psm1
```

このスクリプトは、乱数を生成し、それに合わせてプロバイダー コードを更新します。 `DebugMode` を false に設定しても、ファイル "**$env:SystemDrive\OutputFromTestProviderDebugMode.txt**" の内容は変わりません。

ここで、構成スクリプトで `DebugMode` を **TRUE** に設定します。

```powershell
LocalConfigurationManager
{
    DebugMode = $true
} 
```

上記のスクリプトを再度実行すると、ファイルの内容が毎回異なるものになります。 (`Get-DscConfiguration` を実行して確認できます)。 次に、さらに 2 つの実行の結果を示します (実際にスクリプトを実行した結果は異なる場合があります)。

```powershell
PS C:\> Get-DscConfiguration -CimSession (New-CimSession localhost)
 
onlyProperty                            PSComputerName                         
------------                            --------------                         
20                                      localhost                              
 
PS C:\> Get-DscConfiguration -CimSession (New-CimSession localhost)
 
onlyProperty                            PSComputerName                         
------------                            --------------                         
14                                      localhost
```

## 参照

### 参照先
* [DSC Log Resource (DSC ログ リソース)](logResource.md)

### 概念
* [Build Custom Windows PowerShell Desired State Configuration Resources (カスタム Windows PowerShell Desired State Configuration のビルド)](authoringResource.md)

### その他のリソース
* [Windows PowerShell Desired State Configuration Cmdlets (Windows PowerShell Desired State Configuration のコマンドレット)](https://technet.microsoft.com/en-us/library/dn521624(v=wps.630).aspx)




<!--HONumber=Oct16_HO1-->


