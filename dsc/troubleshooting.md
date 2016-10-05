# DSC222 のトラブルシューティング

>適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

このトピックでは、Desired State Configuration (DSC) スクリプトがエラーなしで実行されるようにする方法を説明します。 ログを効率的に使用してエラーを追跡し、リソース変更の結果を即時に反映するためにキャッシュをリサイクルする方法を理解することで、DSC のトラブルシューティングを効率化できます。 これらの手法を次の 2 つのセクションで説明します。

* スクリプトが実行されない: **DSC ログを使用したスクリプト エラーの診断**
* リソースが更新されない: **キャッシュをリセットする方法**

## スクリプトが実行されない: DSC ログを使用したスクリプト エラーの診断

すべての Windows ソフトウェアと同じく、DSC は[イベント ビューアー](http://windows.microsoft.com/windows/what-information-event-logs-event-viewer)から参照可能な[ログ](https://msdn.microsoft.com/library/windows/desktop/aa363632.aspx)にエラーとイベントを記録します。 これらのログを調べることは、特定の操作が失敗した理由や、今後エラーを防止する方法を理解するために役立ちます。 構成スクリプトの記述は複雑になることがあります。そのため、作成しながらエラーをより簡単に追跡できるように、DSC ログ リソースを使用して DSC 分析イベント ログで構成の進行状況を追跡してください。

## DSC イベント ログの場所

イベント ビューアーでは、DSC イベントは **Applications and Services Logs/Microsoft/Windows/Desired State Configuration** にあります。

対応する PowerShell コマンドレット [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) を次のように実行して、イベント ログを表示することもできます。

```
PS C:\Users> Get-WinEvent -LogName "Microsoft-Windows-Dsc/Operational"
   ProviderName: Microsoft-Windows-DSC
TimeCreated                     Id LevelDisplayName Message                                                                                                  
-----------                     -- ---------------- -------                                                                                                  
11/17/2014 10:27:23 PM        4102 Information      Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
```

上に示すように、DSC のプライマリ ログ名は **Microsoft->Windows->DSC** です (簡略化のため、Windows の下にあるその他のログ名は表示していません)。 完全なログ名を作成するには、プライマリ名にチャネル名を追加します。 DSC エンジンは、主に 3 種類のログに書き込みます。[操作ログ、分析ログ、およびデバッグ ログ](https://technet.microsoft.com/library/cc722404.aspx)です。 分析ログとデバッグ ログは既定でオフになっているため、それらをイベント ビューアーで有効にする必要があります。 これを行うには、Windows PowerShell で「eventvwr」と入力するか、または **[スタート]** ボタン、**[コントロール パネル]**、**[管理ツール]**、**[イベント ビューアー]** の順にクリックして、イベント ビューアーを開きます。 イベント ビューアーの **[表示]** メニューで、**[分析およびデバッグ ログの表示]** をクリックします。 分析チャネルのログ名は **Microsoft-Windows-Dsc/Analytic** で、デバッグ チャネルのログ名は **Microsoft-Windows-Dsc/Debug** です。 次の例に示すように、[wevtutil](https://technet.microsoft.com/library/cc732848.aspx) ユーティリティを使用してログを有効にすることもできます。

```powershell
wevtutil.exe set-log “Microsoft-Windows-Dsc/Analytic” /q:true /e:true
```

## DSC ログに含まれる内容

DSC ログは、メッセージの重要度に基づいて 3 つのログ チャネルに分割されています。 DSC の操作ログにはすべてのエラー メッセージが含まれ、問題の識別に使用できます。 分析ログには、より大量のイベントが含まれ、エラーがどこで発生したかを識別できます。 このチャネルには、詳細メッセージも含まれます (ある場合)。 デバッグ ログには、エラーがどのように発生したかを理解するのに役立つログが含まれています。 DSC イベント メッセージは、すべてのイベント メッセージの先頭が、DSC 操作を一意に表すジョブ ID になるように構成されています。 次の例では、DSC 操作ログに記録された最初のイベントからメッセージを取得しようとしています。

```powershell
PS C:\Users> $AllDscOpEvents=get-winevent -LogName "Microsoft-Windows-Dsc/Operational"
PS C:\Users> $FirstOperationalEvent=$AllDscOpEvents[0]
PS C:\Users> $FirstOperationalEvent.Message
Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
Consistency engine was run successfully. 
```

DSC イベントは、ユーザーが 1 つの DSC ジョブからイベントを集計できるように特定の構造に記録されます。 その構造は次のとおりです。

**ジョブ ID: \ < Guid\ >**
**\ < イベント Message\ >**

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
 
$DscEvents=[System.Array](Get-WinEvent "Microsoft-windows-dsc/operational") `
         + [System.Array](Get-WinEvent "Microsoft-Windows-Dsc/Analytic" -Oldest) `
         + [System.Array](Get-Winevent "Microsoft-Windows-Dsc/Debug" -Oldest)
 
 
<##########################################################################
 Step 4 : Group all logs based on the job ID
###########################################################################>
$SeparateDscOperations=$DscEvents | Group {$_.Properties[0].value}  
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
PS C:\> $SeparateDscOperations  | Where-Object {$_.Group.LevelDisplayName -contains "Error"}
Count Name                      Group                                                                     
----- ----                      -----                                                                     
   38 {5BCA8BE7-5BB6-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord, System.Diagnostics....
```

### 2: 過去 30 分間に実行された操作の詳細

それぞれの Windows イベントのプロパティである `TimeCreated` は、イベントが作成された時刻を示します。 このプロパティを特定の日付/時刻オブジェクトと比較すると、すべてのイベントをフィルター処理できます。

```powershell
PS C:\> $DateLatest=(Get-date).AddMinutes(-30)
PS C:\> $SeparateDscOperations  | Where-Object {$_.Group.TimeCreated -gt $DateLatest}
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
PS C:\> $myFailedEvent=($SeparateDscOperations[0].Group | Where-Object {$_.LevelDisplayName -eq "Error"})
 
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

**xDscDiagnostics** は、コンピューター上の DSC 障害の分析に役立つ 2 つの単純な操作、`Get-xDscOperation` と `Trace-xDscOperation` で構成される PowerShell モジュールです。 これらの関数は、過去の DSC 操作からのすべてのローカル イベント、またはリモート コンピューター上の DSC イベントの識別に役立ちます (有効な資格情報を使用)。 ここでは、開始から終了まで 1 回の一意の DSC 実行を定義するために、DSC 操作という用語を使用します。 たとえば、`Test-DscConfiguration` は独立した DSC 操作です。 同様に、DSC の他のすべてのコマンドレット (`Get-DscConfiguration` や `Start-DscConfiguration` など) をそれぞれ別の DSC 操作として識別できます。 [xDscDiagnostics](https://powershellgallery.com/packages/xDscDiagnostics) PowerShell モジュール (DSC リソース キット) には 2 つのコマンドレットについての説明があり、以下ではより詳細に説明します。 ヘルプを参照するには、`Get-Help <cmdlet name>` を実行します。

## Get-xDscOperation

このコマンドレットでは、1 台または複数のコンピューターで実行される DSC 操作の結果を検索し、それぞれの DSC 操作で生成されたイベントのコレクションが含まれているオブジェクトを返すことができます。 たとえば、次の出力では、3 つのコマンドが実行されました。 1 つ目のコマンドは成功し、他の 2 つのコマンドは失敗しました。 これらの結果が `Get-xDscOperation` の出力にまとめられています。

TODO: Get-xDscOperation 出力を示すこのイメージを置き換えてください。

### パラメーター

* **Newest**: 表示する操作の数を示す整数値を受け取ります。 既定では、最新の 10 個の操作を返します。 TODO: Get-xDscOperation -Newest 5 を表示してください。
* **ComputerName**: 文字列の配列を受け取るパラメーター。それぞれの文字列には、DSC イベント ログ データを収集するコンピューターの名前が含まれています。 既定では、ホスト コンピューターからデータを収集します。 この機能を有効にするには、イベントを収集できるように管理者特権モードにしてからリモート コンピューターで次のコマンドを実行する必要があります。
```powershell
  New-NetFirewallRule -Name "Service RemoteAdmin" -Action Allow
```
* **Credential**: PSCredential 型のパラメーター。ComputerName パラメーターに指定されているコンピューターにアクセスできるようにします。

### 返されるオブジェクト

コマンドレットは、いずれも **Microsoft.PowerShell.xDscDiagnostics.GroupedEvents** 型であるオブジェクトの配列を返します。 この配列内の各オブジェクトは、それぞれ異なる DSC 操作に関連します。 このオブジェクトの既定の表示には、次のプロパティがあります。
* **SequenceID**: 時刻に基づいて DSC 操作に割り当てられた増分番号を示します。 たとえば、最後に実行された操作の SequenceID は 1、最後から 2 番目の DSC 操作の SequenceID は 2、以下同様となります。 この番号は、返される配列内の各オブジェクトの別の識別子となります。
* **TimeCreated**: DSC 操作が開始された日時を示す DateTime 値。
* **ComputerName**: 結果が集計されるコンピューター名。
* **Result**: 値が **Failure** または **Success** になる文字列で、それぞれ DSC 操作でエラーが発生したかどうかを示します。
* **AllEvents**: DSC 操作によって生成されるイベントのコレクションを表すオブジェクト。

たとえば、次の出力が複数のコンピューターから最後の操作の結果を示しています TODO: リモート コンピューターのログを表示する Get-xdscoperation の置換の画像。

## Trace-xDscOperation

このコマンドレットは、イベント、そのイベントの種類、および特定の DSC 操作から生成されたメッセージ出力のコレクションが含まれているオブジェクトを返します。 通常、`Get-xDscOperation` を使用した操作のいずれかでエラーが発生した場合、その操作をトレースすると、どのイベントによってエラーが発生したかがわかります。

### パラメーター

* **SequenceID**: これは、特定のコンピューターに関連するすべての操作に割り当てられる整数値です。 たとえば、シーケンス ID を 4 に指定すると、最後から 4 番目の DSC 操作のトレースが出力されます。

シーケンス ID が指定された Trace-xDscOperation
* **JobID**: これは、操作を一意に識別するために、LCM xDscOperation によって割り当てられる GUID 値です。 ジョブ ID を指定すると、対応する DSC 操作のトレースが出力されます。
  TODO: パラメーターとして JobID を取るように Trace-xDscOperation の画像を置き換えてください。
* **ComputerName** と **Credential**: これらのパラメーターを指定すると、リモート コンピューターからトレースを収集できます。
```powershell
New-NetFirewallRule -Name "Service RemoteAdmin" -Action Allow
```
  TODO: 別のコンピューターで動作するように Trace-xDscOperation の画像を置き換えてください。

`Trace-xDscOperation` では、分析ログ、デバッグ ログ、および操作ログからイベントを集計するため、上記のようにこれらのログを有効にするように、プロンプトで要求されます。

### 返されるオブジェクト

コマンドレットは、いずれも `Microsoft.PowerShell.xDscDiagnostics.TraceOutput` 型であるオブジェクトの配列を返します。 この配列内の各オブジェクトには、次のフィールドが含まれています。
* **ComputerName**: ログが収集されるコンピューターの名前。
* **EventType**: これは、イベントの種類に関する情報が含まれている列挙子型のフィールドです。 次のいずれかとなります。
  - *Operational*: 操作ログからのイベントです。
  - *Analytic*: 分析ログからのイベントです。
  - *Debug*: デバッグ ログからのイベントです。
  - *Verbose*: 実行中に詳細なメッセージとして出力されたイベントです。 詳細メッセージにより、発行されたイベントのシーケンスを容易に識別できます。
  - *Error*: エラー イベントです。 通常、エラー イベントを探すことで、失敗の理由がすばやくわかります。
* **TimeCreated**: DSC によってイベントが記録された日時を示す DateTime 値。
* **Message**: DSC によってイベント ログに記録されたメッセージ。

次に、イベントに関するその他の情報に使用できるものの既定では表示されない、このオブジェクト内のフィールドを示します。

* **JobID**: その DSC 操作に固有のジョブ ID (GUID 形式)。
* **SequenceID**: そのコンピューターでの DSC 操作に固有の SequenceID。
* **Event**: これは、`System.Diagnostics.Eventing.Reader.EventLogRecord` 型で DSC によって記録された実際のイベントです。 これは、コマンドレット `Get-WinEvent` を実行して取得することもできます。 タスク、イベント ID、イベントのレベルなど、その他の情報が含まれています。

また、`Trace-xDscOperation` の出力を変数に保存して、イベントに関する情報を収集することもできます。 次のコマンドを使用すると、特定の DSC 操作のすべてのイベントを表示できます。

```powershell
(Trace-xDscOperation -SequenceID 3).Event
```

これと同じ結果が表示されます、 `Get-WinEvent` コマンドレットは、次の出力など: TODO: 出力何でしょうか。

まず、`Get-xDscOperations` を使用して、コンピューターでの DSC 構成実行のうち、最新のいくつかを一覧表示すると理想的です。 次に、`Trace-xDscOperation` でいずれかの単一の操作を (その SequenceID または JobID を使用して) 調べると、バックグラウンドで何が行われたかがわかります。

## リソースが更新されない: キャッシュをリセットする方法

DSC エンジンは、効率化のために、PowerShell モジュールとして実装されているリソースをキャッシュします。 ただし、リソースを作成すると同時にテストする場合には、このことによって問題が発生することがあります。これは、DSC では、プロセスが再開されるまで、キャッシュされたバージョンを読み込むためです。 新しいバージョンが読み込まれるようにする唯一の方法は、DSC エンジンをホストしているプロセスを明示的に強制終了することです。

同様に、カスタム リソースを追加および変更した後で `Start-DSCConfiguration` を実行した場合、その変更はコンピューターを再起動しないと反映されません。 これは、DSC が WMI Provider Host Process (WmiPrvSE) で動作し、通常、WmiPrvSE のインスタンスは同時にいくつも動作しているためです。 コンピューターを再起動すると、ホスト プロセスが再起動し、キャッシュがクリアされます。

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
PS C:\Users\WinVMAdmin\Desktop> Get-DscLocalConfigurationManager
 
 
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
    return @{onlyProperty = Get-Content -path "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"}
}
function Set-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        $onlyProperty
    )
    "1"|Out-File -PSPath "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"
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
    return @{onlyProperty = Get-Content -path "$env:SystemDrive\OutputFromTestProviderDebugMode.txt"}
}
function Set-TargetResource
{
    param
    (
        [Parameter(Mandatory)]
        `$onlyProperty
    )
    "$newResourceOutput"|Out-File -PSPath C:\OutputFromTestProviderDebugMode.txt
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
PS C:\Users\WinVMAdmin\Desktop> Get-DscConfiguration -CimSession (New-CimSession localhost)
 
onlyProperty                            PSComputerName                         
------------                            --------------                         
20                                      localhost                              
 
PS C:\Users\WinVMAdmin\Desktop> Get-DscConfiguration -CimSession (New-CimSession localhost)
 
onlyProperty                            PSComputerName                         
------------                            --------------                         
14 
```

## 参照

### 参照先
* [DSC Log Resource (DSC ログ リソース)](logResource.md)

### 概念
* [Build Custom Windows PowerShell Desired State Configuration Resources (カスタム Windows PowerShell Desired State Configuration のビルド)](authoringResource.md)

### その他のリソース
* [Windows PowerShell Desired State Configuration Cmdlets (Windows PowerShell Desired State Configuration のコマンドレット)](https://technet.microsoft.com/en-us/library/dn521624(v=wps.630).aspx)


<!--HONumber=Oct16_HO1-->


