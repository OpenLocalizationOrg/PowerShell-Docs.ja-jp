#Windows PowerShell 4.0 に必要な状態の構成ローカル Configuration Manager (LCM)

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

ローカルの Configuration Manager は、Windows PowerShell 必要な状態 Configuration (DSC) エンジンです。 すべてのターゲット ノード上で実行されを DSC 構成のスクリプトに含まれている構成のリソースを呼び出しになります。 このトピックでは、ローカル Configuration Manager のプロパティを一覧表示し、ターゲット ノード上のローカルの Configuration Manager の設定を変更する方法について説明します。

##ローカルの Configuration Manager のプロパティ

設定するかを取得するローカルの Configuration Manager のプロパティを次に示します。

* **AllowModuleOverwrite**: のターゲット ノード上の古いファイルを上書きすることを許可する新しい構成が、構成サーバーからダウンロードするかどうかを制御します。 可能な値は True と false を指定します。
* **証明書 Id**: GUID、証明書の構成にアクセスするための資格情報をセキュリティで保護するために使用します。 詳細については、次を参照してください [Windows PowerShell 必要な状態の設定での資格情報をセキュリティで保護するでしょうか。](http://blogs.msdn.com/b/powershell/archive/2014/01/31/want-to-secure-credentials-in-windows-powershell-desired-state-configuration.aspx)。.
* **ConfigurationID**:「プル」サーバーとして設定する、サーバーから特定の構成ファイルを取得するために使用する GUID のことを示します。 GUID は、適切な構成ファイルにアクセスするようにします。
* **ConfigurationMode**: ローカルの Configuration Manager 実際に適用する方法、構成対象のノードを指定します。 次の値がかかることができます。
   - **ApplyOnly**: このオプションで DSC は構成を適用し、は何もさらに、新しい構成が検出された場合を除き、新しい構成をターゲットに直接送信してノード (「プッシュ」) またはサーバーの「プル」と DSC を構成したかどうかは、検出、新しい構成「プル」サーバーをチェックし、ときにします。 対象のノードの構成は、次のしまいます、アクションは行われません。
   - **ApplyAndMonitor**。 オプションではこの (これが既定です)、DSC によって、ターゲット ノードに直接送られたもの、または"プル"サーバー上で検出されたかどうか、新しい構成のいずれかが適用されます。 その後、ターゲット ノードの構成は、構成ファイルからしまいます、DSC はログの不一致を報告します。 詳細については、DSC のログ記録は、次を参照してください。 [状態の必要な構成でのエラーの診断にイベント ログを使用して](http://blogs.msdn.com/b/powershell/archive/2014/01/03/using-event-logs-to-diagnose-errors-in-desired-state-configuration.aspx)です。
   - **ApplyAndAutoCorrect**: によって、ターゲット ノードに直接送られたもの、または「プル」サーバー上で検出されたかどうか、このオプションを DSC が新しい構成のいずれかを適用します。 その後、ターゲット ノードの構成は、構成ファイルからしまいます、DSC はログに不一致が報告し、構成ファイルに準拠するためのターゲット ノードの構成を調整するには、次のように試行します。
* **ConfigurationModeFrequencyMins**: 分単位で DSC のバック グラウンド アプリケーションでのターゲット ノード上の現在の構成を実装しようとする頻度を表します。 既定値は、15 です。 RefreshMode と共に、この値を設定することができます。 RefreshMode がプルに設定されている場合、ターゲット ノードが RefreshFrequencyMins の設定間隔で構成サーバーに接続し、現在の構成をダウンロードします。 ConfigurationModeFrequencyMins、によって設定された間隔で、RefreshMode 値に関係なく、一貫性エンジンは適用対象のノードにダウンロードされた最新の構成します。 RefreshFrequencyMins を整数に設定する必要があります ConfigurationModeFrequencyMins の倍数です。
* **資格情報**: (Get-credential) と同じ資格情報を示すようにリモート リソースにアクセスする構成のサーバーに接続するために必要です。
* **DownloadManagerCustomData**: ダウンロード マネージャーに固有のカスタム データを含む配列を表します。
* **DownloadManagerName**: 構成とモジュールのダウンロード マネージャーの名前を示します。
* **RebootNodeIfNeeded**: ターゲット ノード上の特定の構成変更に変更を適用するのには再起動する必要があります。 値を持つ **True**, 、されましたが、構成するとすぐに、このプロパティは、ノードを再起動は警告メッセージを表示することがなく、完全に適用します。 場合 **False** (既定値) は、構成が完了しますが、変更を有効にするため、ノードを手動で再起動する必要があります。
* **RefreshFrequencyMins**:「プル」サーバーをセットアップするときに使用します。 頻度 (分) をローカルの Configuration Manager が、現在の構成をダウンロードする「プル」サーバーに接続を表します。 ConfigurationModeFrequencyMins と共に、この値を設定することができます。 RefreshMode がプルに設定されている場合、ターゲット ノードが RefreshFrequencyMins の設定間隔で「プル」サーバーに接続し、現在の構成をダウンロードします。 ConfigurationModeFrequencyMins によって設定、間隔で一貫性のエンジンでは、ターゲット ノードにダウンロードされた最新の構成が、適用されます。 RefreshFrequencyMins が整数に設定されていない場合 ConfigurationModeFrequencyMins、システムの倍数を使用すると丸めるされます。 既定値は、30 です。
* **RefreshMode**。 指定できる値は **プッシュ** (既定値) および **プル**です。 構成では、「プッシュ」には任意のクライアント コンピューターを使用して、各ターゲット ノード上の構成ファイル配置する必要があります。 「プル」モードでは、「プル」サーバーに連絡し、構成ファイルにアクセスするローカル Configuration Manager を設定する必要があります。

###ローカルの Configuration Manager の設定を更新する例

ターゲット ノードのローカルの Configuration Manager の設定を更新するには含めることによって、 **LocalConfigurationManager** の次の例に示すように、スクリプトでは、構成、ノードのブロックの内部ブロックします。

```powershell
Configuration ExampleConfig
{
    Node “Server001”
    {
        LocalConfigurationManager
        {
            ConfigurationID = "646e48cb-3082-4a12-9fd9-f71b9a562d4e"
            ConfigurationModeFrequencyMins = 45
            ConfigurationMode = "ApplyAndAutocorrect"
            RefreshMode = "Pull"
            RefreshFrequencyMins = 90
            DownloadManagerName = "WebDownloadManager"
            DownloadManagerCustomData = (@{ServerUrl="https://$PullServer/psdscpullserver.svc"})
            CertificateID = "71AA68562316FE3F73536F1096B85D66289ED60E"
            Credential = $cred
            RebootNodeIfNeeded = $true
            AllowModuleOverwrite = $false
        }
# One or more resource blocks can be added here
    }
}

# The following line invokes the configuration and creates a file called Server001.meta.mof at the specified path
ExampleConfig -OutputPath "c:\users\public\dsc"  
```

前の例で、スクリプトを実行するを指定して、目的の設定を格納するための MOF ファイルが生成されます。 設定を適用するには、使用することができます、 **セット DscLocalConfigurationManager** コマンドレットは、次の例で示すようにします。

```powershell
Set-DscLocalConfigurationManager -Path "c:\users\public\dsc"
```

> **注**: の **パス** パラメーターで指定した同じパスを指定する必要があります、 **OutputPath** パラメーターの前の例では、構成が呼び出されたときにします。

現在のローカルの Configuration Manager の設定を表示するには、使用することができます、 **Get DscLocalConfigurationManager** コマンドレットです。 パラメーターなしでは、このコマンドレットを呼び出すと、既定ではこれが発生を実行するノードのローカルの Configuration Manager 設定します。 別のノードを指定する、 **CimSession** パラメーターをこのコマンドレットを使用します。



