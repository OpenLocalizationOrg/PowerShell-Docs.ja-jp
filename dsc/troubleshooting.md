#DSC のトラブルシューティング

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

このトピックでは、エラーなしで実行するように必要な状態 Configuration (DSC) スクリプトを取得するための方法を説明します。 ログを効果的に使用してエラー、および、リソースの変更の即時の結果を表示するキャッシュを再利用する方法を理解することを追跡することができます DSC をより効果的にトラブルシューティングを行う。 2 つのセクションでは、これらの技術がについて説明します。

* 私のスクリプトは実行されません **スクリプト エラーを診断するログに記録を使用して DSC**。
* 自分のリソースを更新しません **、キャッシュをリセットする方法**。

##私のスクリプトは実行されませんスクリプトのエラーを診断する DSC を使用してログに記録。

エラーとイベントのすべての Windows ソフトウェアと同様に DSC を記録 [ログ](https://msdn.microsoft.com/library/windows/desktop/aa363632.aspx) から表示できますが、 [イベント ビューアー](http://windows.microsoft.com/windows/what-information-event-logs-event-viewer)です。 これらのログを確認すると、特定の操作が失敗した理由と、今後エラーを回避する方法を理解することができます。 構成スクリプトの記述は注意が必要で、これを行うために追跡エラー作成者を簡単に DSC 分析のイベント ログで、構成の進行状況を追跡するために、DSC ログ リソースを使用することはできます。

##DSC イベント ログを参照しているでしょうか。

DSC イベントが、イベント ビューアーで、: **アプリケーションとサービス ログ、Microsoft、Windows と望ましい状態の構成**

対応する PowerShell コマンドレット、 [Get-winevent](https://technet.microsoft.com/library/hh849682.aspx), 、イベント ログの表示を実行することもできます。

```
PS C:\Users> Get-WinEvent -LogName "Microsoft-Windows-Dsc/Operational"
   ProviderName: Microsoft-Windows-DSC
TimeCreated                     Id LevelDisplayName Message                                                                                                  
-----------                     -- ---------------- -------                                                                                                  
11/17/2014 10:27:23 PM        4102 Information      Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
```

DSC のプライマリのログの名前は、上記のように **-> Windows の Microsoft DSC を ->** (Windows の下にある場合は、その他のログ名は表示されないここでは簡潔にするため)。 プライマリの名前は、完全なログ名を作成するチャネル名に追加されます。 DSC エンジンは、主に次の 3 つの種類のログに書き込みます。 [運用、分析、およびデバッグ ログ](https://technet.microsoft.com/library/cc722404.aspx)です。 以降、分析とデバッグ ログは、既定でオフになって、イベント ビューアーで有効にする必要があります。 これを行うには、Windows powershell。「eventvwr」と入力してイベント ビューアーを開きます。または、クリックして、 **開始** ] ボタン、[ **コントロール パネルの [**, 、] をクリックして **管理ツール**, 、順にクリック **イベント ビューアー**です。  **ビュー** イベント ビューアーで、メニューをクリックして **[分析およびデバッグ ログ**です。 分析のチャネルのログの名前は **Microsoft Windows-Dsc 分析/**, 、デバッグ チャネルは **Microsoft Windows-Dsc/デバッグ**です。 使用することも、 [wevtutil](https://technet.microsoft.com/library/cc732848.aspx) ユーティリティを次の例で示すように、ログを有効にします。

```powershell
wevtutil.exe set-log “Microsoft-Windows-Dsc/Analytic” /q:true /e:true
```

##DSC ログの格納

DSC ログは、メッセージの重要度に基づいて、次の 3 つのログ チャネル上で分割されます。 DSC での操作のログは、すべてのエラー メッセージが含まれています、問題を識別するために使用できます。 分析ログにはイベントのより高いボリュームがあり、エラーが発生するを特定できます。 このチャネルには詳細メッセージが含まれています (指定されている場合)。 デバッグ ログには、エラーが発生する方法を理解するのに役立つログが含まれています。 DSC イベント メッセージは、一意に、操作を表す DSC ジョブ ID を持つすべてのイベント メッセージを開始するように構成されています。 次の例は、運用 DSC ログに記録する最初のイベントからのメッセージを取得しようとします。

```powershell
PS C:\Users> $AllDscOpEvents=get-winevent -LogName "Microsoft-Windows-Dsc/Operational"
PS C:\Users> $FirstOperationalEvent=$AllDscOpEvents[0]
PS C:\Users> $FirstOperationalEvent.Message
Job {02C38626-D95A-47F1-9DA2-C1D44A7128E7} : 
Consistency engine was run successfully. 
```

DSC イベントは、集計のイベントに DSC の 1 つのジョブからのユーザーができる特定の構造体に記録されます。 構造は次のとおりです。

**ジョブ ID: \ < Guid\ >**
**\ < イベント Message\ >**

##1 つの DSC 操作からのイベントの収集

DSC のイベント ログには、さまざまな DSC 操作によって生成されたイベントが含まれます。 ただし、通常おくの詳細に 1 つだけ特定の操作を懸念します。 DSC のすべてのログは、すべて DSC 操作には一意であるジョブの ID プロパティによってグループ化することができます。 ジョブの ID は、最初のプロパティ値が DSC のすべてのイベントとして表示されます。 次の手順では、すべてのイベントをグループ化された配列構造を積算する方法について説明します。

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

ここでは、変数 `$SeparateDscOperations` ジョブ Id でグループ化されたログが含まれています。 この変数の場合は、各配列要素では、ログに関する詳細情報へのアクセスを許可する、別の DSC 操作によってログに記録するイベントのグループを表します。

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

変数内のデータを抽出する `$SeparateDscOperations` を使用して [Where-object](https://technet.microsoft.com/library/ee177028.aspx)です。 DSC をトラブルシューティングのためのデータを抽出する 5 つのシナリオを次に示します。

###1: 操作のエラー

すべてのイベントには、[重大度レベル] が必要がある (https://msdn.microsoft.com/library/dd996917 (v=vs.85))。 この情報は、エラー イベントを識別するために使用できます。

```
PS C:\> $SeparateDscOperations  | Where-Object {$_.Group.LevelDisplayName -contains "Error"}
Count Name                      Group                                                                     
----- ----                      -----                                                                     
   38 {5BCA8BE7-5BB6-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord, System.Diagnostics....
```

###2: 操作の詳細については、最後の 30 分で実行

`TimeCreated`, 、すべての Windows イベントのプロパティは、イベントが作成された時刻を示します。 特定の日付と時刻のオブジェクトには、このプロパティを比較することは、すべてのイベントをフィルター処理は使用できます。

```powershell
PS C:\> $DateLatest=(Get-date).AddMinutes(-30)
PS C:\> $SeparateDscOperations  | Where-Object {$_.Group.TimeCreated -gt $DateLatest}
Count Name                      Group                                                                     
----- ----                      -----                                                                     
    1 {6CEC5B09-5BB0-11E3-BF... {System.Diagnostics.Eventing.Reader.EventLogRecord}   
```

###3: 最新バージョンの操作からのメッセージ

最新バージョンの操作が、配列のグループの最初のインデックスに格納されている `$SeparateDscOperations`です。 インデックス 0 のグループのメッセージのクエリを実行するには、最新バージョンの操作のすべてのメッセージが返されます。

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

###4: 最近の失敗した操作のログに記録するエラー メッセージ

`$SeparateDscOperations [0]。グループ` 最新バージョンの操作のイベントのセットが含まれています。 実行、 `Where-object` イベントをフィルター処理するコマンドレットが、レベルの表示名に基づいています。 結果に格納されます、 `$myFailedEvent` イベント メッセージを取得する変数の場合、それ以上になることが分析します。

```powershell
PS C:\> $myFailedEvent=($SeparateDscOperations[0].Group | Where-Object {$_.LevelDisplayName -eq "Error"})

PS C:\> $myFailedEvent.Message
Job {5BCA8BE7-5BB6-11E3-BF41-00155D553612} : 
DSC Engine Error : 
 Error Message Current configuration does not exist. Execute Start-DscConfiguration command with -Path pa
rameter to specify a configuration file and create a current configuration first. 
Error Code : 1 
```

###5: 特定のジョブ ID の生成されたすべてのイベント

`$SeparateDscOperations` それぞれが固有のジョブの ID と名前を持つグループの配列には 実行して、 `Where-object` コマンドレットでは、それらの特定のジョブ ID を持つイベントのグループを抽出することができます。

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

##ログに記録 xDscDiagnostics を使用して、DSC を分析するには

**xDscDiagnostics** PowerShell モジュールは、コンピューター上の DSC 不具合を分析するのに役立つ 2 つの単純な操作で構成される: `Get xDscOperation` と `トレース xDscOperation`です。これらの関数は、(有効な資格情報は) の過去の DSC 操作からのすべてのローカル イベントまたはリモート コンピューター上の DSC イベントを識別するのに役立ちます。ここで、という用語は、DSC 操作を 1 回一意 DSC の実行の開始から最後までを定義します。たとえば、 `テスト DscConfiguration` DSC、別の操作になります。同様に、すべての他のコマンドレットが DSC (など `Get DscConfiguration`, 、`開始 DscConfiguration`, 」など) 各 DSC の個別の操作として識別できる可能性があります。2 つのコマンドレットの説明で [xDscDiagnostics](https://powershellgallery.com/packages/xDscDiagnostics) PowerShell モジュール (DSC リソース キット) と、詳細については、以下にします。実行して、ヘルプが利用可能な `Get-help < コマンドレット名 >`です。

##Get xDscOperation

このコマンドレットでは、1 つまたは複数のコンピューターで実行される DSC 操作の結果を見つけることができ、各 DSC 操作によって生成されたイベントのコレクションを格納しているオブジェクトを返します。 たとえば、次の出力に実行された 3 つのコマンドはします。 最初の 1 つが渡されると、し、[他の 2 つのできませんでした。 これらの結果の出力をまとめた `Get xDscOperation`です。

TODO: Get xDscOperation 出力を示しています。 このイメージを置き換える

###パラメーター

* **昇順**: を表示する操作の数を示す整数値を受け取ります。 既定では、最新の 10 の操作を返します。 たとえば、
   TODO: Get xDscOperation の表示-最新 5
* **ComputerName**: パラメーターをそれぞれから DSC イベント ログのデータを収集するには、コンピューターの名前を含む文字列の配列を受け取る。 既定では、ホスト マシンからデータを収集します。 この機能を有効にするには、管理者特権モードでは、リモートの各コンピューターで次のコマンドを実行する必要がありますできるように、イベントのコレクションが許可されます
```powershell
  New-NetFirewallRule -Name "Service RemoteAdmin" -Action Allow
```
* **資格情報**: であるパラメーターの型の PSCredential、ComputerName パラメーターで指定されているコンピューターにアクセスできることができます。

###返されるオブジェクト

コマンドレットは、返すオブジェクトの配列型の各 **Microsoft.PowerShell.xDscDiagnostics.GroupedEvents**です。 この配列内の各オブジェクトは、別の DSC 操作に関係します。 このオブジェクトの既定の表示には、次のプロパティ
* **SequenceID**: 増分時間に基づいて DSC 操作に割り当てられている数を指定します。 たとえば、SequenceID 1 と、最後に実行された操作が必要があります、DSC 操作の最後から 2 番目は、2 の SequenceID されなどです。 この番号は、返される配列内の各オブジェクトのもう 1 つの識別子です。
* **TimeCreated**: DSC 操作を開始したときに完了したことを示す A の DateTime 値。
* **ComputerName**。 コンピューター名を、結果が集計されます。
* **結果**: 値を持つ文字列 **エラー** または **成功** その DSC 操作が表示されていましたエラーかを示すそれぞれします。
* **AllEvents**: DSC 操作によってイベントのコレクションを表すオブジェクトが生成されます。

たとえば、次の出力は、複数のコンピューターからの最後の操作の結果を示しています。
  リモート コンピューターのログを表示するには、Get-xDscOperation TODO: 置換の画像

##トレース xDscOperation

このコマンドレットでは、イベント、そのイベントの種類、および特定の DSC 操作から生成されたメッセージの出力のコレクションを含むオブジェクトを返します。 通常、見つかった場合、エラーを使用して、操作のいずれかで `Get xDscOperation`, が、エラーが発生するイベントのことを確認するには、その操作をトレースします。

###パラメーター

* **SequenceID**。 これは、特定のコンピュータに関連するすべての操作に割り当てられている整数値です。 指定して、シーケンス ID は、4、最後の 4 番目のあった DSC 操作のトレースが出力にします。

シーケンス ID が指定された Trace-xDscOperation
* **JobID**。 これは、操作を一意に識別するために、LCM xDscOperation によって割り当てられた GUID 値。 ジョブ Id を指定すると、対応する DSC 操作のトレースは出力を示します。
   TODO: 置換の画像をトレース xDscOperation をパラメーターとしてのジョブ Id を取得します。
* **ComputerName** と **資格情報**: これらのパラメーターは、リモート コンピューターから収集するトレースを許可します。
```powershell
New-NetFirewallRule -Name "Service RemoteAdmin" -Action Allow
```
  TODO: トレース xDscOperation の異なるコンピューター上で実行の画像を置換します。

ので注意してください、 `トレース xDscOperation` 分析、デバッグ、および運用ログからイベントを集計、上記のように、これらのログを有効にするように求められます。

###返されるオブジェクト

コマンドレットは、それぞれの種類のオブジェクトの配列を返します `Microsoft.PowerShell.xDscDiagnostics.TraceOutput`です。 この配列内の各オブジェクトには、次のフィールドが含まれています。
* **ComputerName**: ログの収集場所からコンピューターの名前。
* **EventType**。 これはイベントの種類の情報を含む列挙子の種類のフィールドです。 次のいずれかの可能性があります。
   - *運用*: イベントは、操作ログをします。
   - *分析*: イベントは、分析ログをします。
   - *デバッグ*: イベントは、デバッグ ログをします。
   - *Verbose*: イベントの出力としての実行中に、詳細メッセージ。 詳細メッセージを簡単に公開されているイベントのシーケンスを識別するためにします。
   - *エラー*: エラー イベントです。 エラー イベントを探しには、通常は、すばやくに、エラーの原因を検索することができます。
* **TimeCreated**: A DSC によってイベントが記録されたときを示す値。
* **メッセージ**: DSC によって、イベント ログに記録されたメッセージ。

詳細については、イベントを使用できますが、既定では表示されませんが、このオブジェクト内のフィールドを次に示します。

* **JobID**: その DSC 操作に固有ジョブ ID (GUID 形式)。
* **SequenceID**: [SequenceID がそのコンピューターでは、その DSC 操作に固有です。
* **イベント**。 これは、実際のイベントの種類の DSC によってログに記録 `System.Diagnostics.Eventing.Reader.EventLogRecord`です。 これも、取得したことが、コマンドレットを実行して `Get-winevent`です。 詳細についてには、タスク、eventID には、イベントのレベルなどが含まれています。

出力を保存することでは、イベント情報を収集する代わりに、 `トレース xDscOperation` を変数にします。 次のコマンドを使用するには、特定の DSC 操作のすべてのイベントを表示します。

```powershell
(Trace-xDscOperation -SequenceID 3).Event
```

これと同じ結果が表示されます、 `Get-winevent` コマンドレットは、次に示す出力など。
  TODO: どのような出力でしょうか。

理想的には、まず用途 `Get xDscOperations` 、最後にいくつかの DSC を一覧表示する、構成は、マシンで実行します。 次を (その sequenceID またはジョブ Id を使用)、1 回の操作を調べることができます `トレース xDscOperation` をバック グラウンドで何を検出します。

##自分のリソースを更新しませんキャッシュをリセットする方法。

DSC エンジンでは、効率を高めるのための PowerShell モジュールとして実装されているリソースをキャッシュします。 ただし、この問題が発生するリソースを作成し、DSC は、プロセスが再起動されるまで、キャッシュされたバージョンが読み込まため同時にテストする場合。 DSC を読み込み、新しいバージョンを作成する唯一の方法では、明示的に DSC エンジンをホストするプロセスを強制終了です。

同様に、実行 `開始 DSCConfiguration`, を追加して、カスタム リソースを変更した後、変更が実行されない限り、または、コンピューターを再起動するまで、します。 DSC は、WMI プロバイダーのホスト プロセス (WmiPrvSE) で実行され、通常は、同時に実行して WmiPrvSE の多くのインスタンスがあるためにです。 再起動すると、ホスト プロセスを再起動し、キャッシュをクリアします。

正常に構成を再利用を再起動しなくても、キャッシュをクリアするには、停止して、し、ホスト プロセスを再起動する必要があります。 これは、プロセスを識別する、停止するか、および再起動でインスタンスごとの単位で行うことができます。 または、使用することができます `DebugMode`, PowerShell DSC リソースを再読み込みを以下に示すように、します。

DSC エンジンをホストしているプロセスを識別され、インスタンスごとは DSC エンジンをホストしている WmiPrvSE のプロセス ID を一覧表示できます。 更新するには、プロバイダー、WmiPrvSE プロセスが、次のコマンドを使用してさらに実行を停止 **開始 DscConfiguration** もう一度です。

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

##Debugmode の使用

DSC ローカル Configuration Manager (LCM) を使用することができます `DebugMode` を常にホスト プロセスを再起動したときに、キャッシュをオフにします。 設定すると **TRUE**, 、常に、PowerShell DSC リソースを再読み込みするエンジンになります。 完了した時点、リソースを作成するには、設定できるに **FALSE** し、エンジンが、モジュールのキャッシュの動作に戻ります。

次に、デモを表示する方法 `DebugMode` 、キャッシュを自動的に更新することができます。 最初に、既定の構成を見てみましょう。

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

いることがわかります `DebugMode` に設定されている **FALSE**です。

設定する、 `DebugMode` デモについては、次の PowerShell のリソースを使用します。

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

呼ばれる上記のリソースを使用して構成を作成、 `TestProviderDebugMode`:

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

ファイルの内容:"**$env:SystemDrive\OutputFromTestProviderDebugMode.txt**"は **1**です。

ここで、次のスクリプトを使用して、プロバイダー コードを更新します。

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

このスクリプトでは、ランダムな数値を生成し、それに応じて cade のプロバイダーを更新します。  `DebugMode` ファイルの内容を false に設定"**$env:SystemDrive\OutputFromTestProviderDebugMode.txt**"が変更されたことはありません。

これで、設定 `DebugMode` に **TRUE** 構成スクリプトで。

```powershell
LocalConfigurationManager
{
    DebugMode = $true
} 
```

上記のスクリプトを再実行するとき、毎回、ファイルのコンテンツが別であるが表示されます。 (実行することができます `Get DscConfiguration` 確認できるようにする)。 (結果を異なる場合があります、スクリプトを実行するときに) 2 つの追加の実行の結果を次に示します。

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

##関連項目

###参照

* [DSC ログのリソース](logResource.md)

###概念

* [カスタムの Windows PowerShell が必要な状態の構成のリソースを作成します。](authoringResource.md)

###その他のリソース

* [Windows PowerShell には、状態の構成のコマンドレットが必要な](https://technet.microsoft.com/en-us/library/dn521624 (v=wps.630).aspx)




