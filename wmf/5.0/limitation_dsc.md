# Desired State Configuration (DSC) の既知の問題と制限事項

重要な変更: WMF 5.0 RTM をインストールした後、DSC 構成でパスワードの暗号化/暗号化の解除に使用される証明書が機能しない場合がある
--------------------------------------------------------------------------------------------------------------------------------

WMF 4.0 および WMF 5.0 Preview リリースでは、DSC の構成内で 121 文字を超える長さのパスワードを使用できません。 DSC では、長い強力なパスワードが必要な場合でも、短いパスワードを使用する必要がありました。 この重要な変更により、DSC 構成で任意の長さのパスワードを使用できます。

**解決策:** データの暗号化またはキーの暗号化のキーの使用法と、ドキュメントの暗号化拡張キー使用法 (1.3.6.1.4.1.311.80.1) を含む証明書を再作成します。 Technet 記事 <https://technet.microsoft.com/en-us/library/dn807171.aspx> に詳細な情報が記載されています。


WMF 5.0 RTM をインストールした後、DSC のコマンドレットが失敗することがある
------------------------------------------------------------------------------------
WMF 5.0 RTM をインストールした後、Start-DscConfiguration および他の DSC コマンドレットが次のエラーで失敗することがあります。
```powershell
    LCM failed to retrieve the property PendingJobStep from the object of class dscInternalCache .
    + CategoryInfo : ObjectNotFound: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : MI RESULT 6
    + PSComputerName : localhost
```

**解決策:** 管理者特権の PowerShell セッション (管理者として実行) で、次のコマンドを実行して DSCEngineCache.mof を削除します。
    
```powershell
Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof
```


WMF 5.0 RTM を WMF 5.0 Production Preview の上にインストールした場合、DSC コマンドレットが機能しない場合がある。
------------------------------------------------------
**解決策:** 管理者特権の PowerShell セッション (管理者として実行) で、次のコマンドを実行します。
```powershell
    mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof
```


Get-DscConfiguration を DebugMode で使用しているときに LCM が不安定な状態になることがある
-------------------------------------------------------------------------------

LCM が DebugMode の場合、CTRL + C キーを押して Get-DscConfiguration の処理を停止すると、DSC コマンドレットの大部分が機能しないような不安定な状態になることがあります。

**解決策:** Get-DscConfiguration コマンドレットのデバッグ中に CTRL + C キーを押さないようにします。


Stop-DscConfiguration が DebugMode でハングすることがある
------------------------------------------------------------------------------------------------------------------------
LCM が DebugMode の場合、Get-DscConfiguration で開始された操作を停止しようとすると Stop-DscConfiguration がハングする場合があります。

**解決策:** Get-DscConfiguration で開始された操作のデバッグを「[DSC リソース スクリプトのデバッグ](#dsc-resource-script-debugging)」セクションの説明に従って終了します。


DebugMode で詳細なエラー メッセージが表示されない
-----------------------------------------------------------------------------------
LCM が DebugMode の場合は、DSC リソースから詳細なエラー メッセージが表示されません。

**解決策:** リソースからの詳細なメッセージを表示するには、*DebugMode* を無効にします。


Get-DscConfigurationStatus コマンドレットで Invoke-DscResource 操作を取得できない
--------------------------------------------------------------------------------------
Invoke-DscResource コマンドレットを使用してリソースのメソッドを直接呼び出した後、このような操作のレコードを Get-DscConfigurationStatus によって取得することはできません。

**解決策:** なし。


Get-DscConfigurationStatus でプル サイクル操作がタイプ *Consistency* として返される
---------------------------------------------------------------------------------
ノードがプル更新モードに設定されている場合、プル操作を実行するたびに、Get-DscConfigurationStatus コマンドレットで操作のタイプが *Initial* ではなく *Consistency* としてレポートされます。

**解決策:** なし。

Invoke-DscResource コマンドレットで、メッセージが生成された順序で返されない
---------------------------------------------------------------------------------
Invoke-DscResource コマンドレットでは、詳細、警告、およびエラー メッセージは LCM または DSC リソースによって生成された順序で返されません。

**解決策:** なし。


Invoke-DscResource と共に使用すると、DSC リソースを簡単にデバッグできない
-----------------------------------------------------------------------
LCM がデバッグ モードで実行されている場合 (詳細については「[DSC リソース スクリプトのデバッグ](#dsc-resource-script-debugging)」を参照)、Invoke-DscResource コマンドレットはデバッグ用に接続する実行空間に関する情報を提供しません。
**解決策:** **Get-PSHostProcessInfo**、**Enter-PSHostProcess**、**Get-Runspace**、および **Debug-Runspace** コマンドレットを使用して実行空間を検出して接続し、DSC リソースをデバッグします。

```powershell
# Find all the processes hosting PowerShell
Get-PSHostProcessInfo

ProcessName ProcessId AppDomainName
----------- --------- -------------
powershell 3932 DefaultAppDomain
powershell_ise 2304 DefaultAppDomain
WmiPrvSE 3396 DscPsPluginWkr_AppDomain

# Enter the process that is hosting DSC engine (WMI process with DscPsPluginWkr_Appdomain)
Enter-PSHostProcess -Id 3396 -AppDomainName DscPsPluginWkr_AppDomain

# Find all the available rusnspaces in that process
Get-Runspace

Id Name ComputerName Type State Availability
-- ---- ------------ ---- ----- ------------
2 Runspace2 localhost Local Opened InBreakpoint
5 RemoteHost localhost Local Opened Busy

# Debug the runspace that is in **InBreakpoint** availability state
Debug-Runspace -Id 2
```


同じノードのさまざまな部分構成ドキュメントが同じリソース名を持つことができない
------------------------------------------------------------------------------------------

1 つのノード上に展開される複数の部分構成では、リソース名が同一であることが実行時エラーの原因となります。

**解決策:** 異なる部分構成では、同じリソースでも異なる名前を使用します。


Start-DscConfiguration -UseExisting が -Credential で機能しない
------------------------------------------------------------------

Start-DscConfiguration を -UseExisting パラメーターと共に使用すると、-Credential パラメーターが無視されます。 DSC では、既定のプロセス ID を使用して操作を続行します。 これにより、リモート ノードで処理を続行するために別の資格情報が必要になった場合にエラーが発生します。

**解決策:** リモート DSC 操作に CIM セッションを使用します。
```powershell
$session = New-CimSession -ComputerName $node -Credential $credential
Start-DscConfiguration -UseExisting -CimSession $session
```


DSC 構成内のノード名としての IPv6 アドレス
--------------------------------------------------
DSC 構成スクリプトでのノード名としての IPv6 アドレスは、このリリースではサポートされていません。

**解決策:** なし。


クラスベースの DSC リソースのデバッグ
--------------------------------------
クラスベースの DSC リソースのデバッグは、このリリースではサポートされていません。

**解決策:** なし。


DSC クラスベース リソースの $script スコープで定義された変数と関数が、DSC リソースへの複数の呼び出しで保持されない 
-------------------------------------------------------------------------------------------------------------------------------------

変数または関数が $script スコープで定義されたクラスベース リソースが構成で使用されている場合、Start-DSCConfiguration への複数の連続した呼び出しは失敗します。

**解決策:** すべての変数と関数を DSC リソース クラス自体で定義します。 $script スコープの変数や関数を使用しません。


リソースが PSDscRunAsCredential を使用している場合の DSC リソースのデバッグ
----------------------------------------------------------------------
構成でリソースが *PSDscRunAsCredential* プロパティを使用している場合の DSC リソースのデバッグは、このリリースではサポートされていません。

**解決策:** なし。


PsDscRunAsCredential が DSC 複合リソースでサポートされない
----------------------------------------------------------------

**解決策:** 利用可能な場合は、資格情報プロパティを使用します。 例 ServiceSet および WindowsFeatureSet


*Get-DscResource -Syntax* が PsDscRunAsCredential correctly を正しく反映しない
-------------------------------------------------------------------------
リソースで必須とマークされていない場合、またはサポートされていない場合、Get-DscResource -Syntax は PsDscRunAsCredential を正しく反映しません。

**解決策:** なし。 ただし、ISE で構成を作成すると、IntelliSense の使用時に、PsDscRunAsCredential プロパティに関する正しいメタデータが反映されます。


WindowsOptionalFeature を Windows 7 で使用できない
-----------------------------------------------------

WindowsOptionalFeature DSC リソースは、Windows 7 では使用できません。 このリソースには、Windows 8 以降のリリースの Windows オペレーティング システムで利用可能な DISM コマンドレット、および DISM モジュールが必要です。

クラス ベースの DSC リソースでは、Import-DscResource -ModuleVersion が正常に動作しない場合があります。   
------------------------------------------------------------------------------------------
コンパイル ノードにクラス ベースの DSC リソース モジュールが複数ある場合、`Import-DscResource -ModuleVersion` から指定されたバージョンが取得されず、次のコンパイル エラーが発生します。

```
ImportClassResourcesFromModule : Exception calling "ImportClassResourcesFromModule" with "3" argument(s): "Keyword 'MyTestResource' already defined in the configuration."
At C:\Windows\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PSDesiredStateConfiguration.psm1:2035 char:35
+ ... rcesFound = ImportClassResourcesFromModule -Module $mod -Resources $r ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [ImportClassResourcesFromModule], MethodInvocationException
    + FullyQualifiedErrorId : PSInvalidOperationException,ImportClassResourcesFromModule
```

**解決策:** 次のように `RequiredVersion` キーを指定して `-ModuleName` に *ModuleSpecification* オブジェクトを定義することで、必要なバージョンをインポートします。
``` PowerShell  
Import-DscResource -ModuleName @{ModuleName='MyModuleName';RequiredVersion='1.2'}  
```  

レジストリ リソースなどの一部の DSC リソースは、要求の処理に長い時間がかかる場合があります。
--------------------------------------------------------------------------------------------------------------------------------

**解決方法 1:** 次のフォルダーを定期的にクリーンアップするタスクのスケジュールを作成します。
``` PowerShell 
$env:windir\system32\config\systemprofile\AppData\Local\Microsoft\Windows\PowerShell\CommandAnalysis 
```

**解決方法 2:** 構成の最後に *CommandAnalysis* フォルダーをクリーンアップするように DSC 構成を変更します。
``` PowerShell
Configuration $configName
{

   # User Data
    Registry SetRegisteredOwner
    {
        Ensure = 'Present'
        Force = $True
        Key = $Node.RegisteredKey
        ValueName = $Node.RegisteredOwnerValue
        ValueType = 'String'
        ValueData = $Node.RegisteredOwnerData
    }
    #
    # Script to delete the config 
    #
    script DeleteCommandAnalysisCache
    {
        DependsOn="[Registry]SetRegisteredOwner"
        getscript="@{}"
        testscript = 'Remove-Item -Path $env:windir\system32\config\systemprofile\AppData\Local\Microsoft\Windows\PowerShell\CommandAnalysis -Force -Recurse -ErrorAction SilentlyContinue -ErrorVariable ev | out-null;$true'
        setscript = '$true'
    }
}
```


<!--HONumber=Oct16_HO1-->


