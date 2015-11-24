#プルを PowerShell 4.0 での構成の ID を使用してクライアントの設定

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

対象の各ノードでは、プル モードを使用するように指示して、構成を取得するプルのサーバーと通信できる URL を指定します。 これを行うには、必要な情報を使用してローカルの Configuration Manager (LCM) を構成する必要があります。 LCM を構成するのには、特殊な種類の"metaconfiguration"と呼ばれる構成を作成します。 LCM を構成する方法の詳細については、次を参照してください [Windows PowerShell 4.0 必要な状態構成ローカル Configuration Manager](metaConfig4.md)。

次のスクリプトでは、"PullServer"をという名前のサーバーからプルの構成に、LCM を構成します。

```powershell
Configuration SimpleMetaConfigurationForPull 
{ 
    LocalConfigurationManager 
    { 
        ConfigurationID = "1C707B86-EF8E-4C29-B7C1-34DA2190AE24";
        RefreshMode = "PULL";
        DownloadManagerName = "WebDownloadManager";
        RebootNodeIfNeeded = $true;
        RefreshFrequencyMins = 15;
        ConfigurationModeFrequencyMins = 30; 
        ConfigurationMode = "ApplyAndAutoCorrect";
        DownloadManagerCustomData = @{ServerUrl = "http://PullServer:8080/PSDSCPullServer/PSDSCPullServer.svc"; AllowUnsecureConnection = “TRUE”}
    } 
} 
SimpleMetaConfigurationForPull -Output "."
```

スクリプトでは、 **DownloadManagerCustomData** パス、セキュリティ保護されていない接続が可能に (この例では) については、プル サーバーの URL。

という名前の新しい出力フォルダーを作成した後、このスクリプトが実行される、 **SimpleMetaConfigurationForPull** あります metaconfiguration の MOF ファイルを格納します。

構成を適用するには、次のように使用します。 **セット DscLocalConfigurationManager** のパラメーターを持つ **ComputerName** ("localhost"を使用して) と **パス** (対象のノードの localhost.meta.mof ファイルの場所のパス)。 たとえば、次のように入力します。
```powershell
Set-DSCLocalConfigurationManager –ComputerName localhost –Path . –Verbose.
```

##構成の ID

スクリプトのセット、 **ConfigurationID** をこの目的のために以前作成した GUID LCM のプロパティ (を使用して GUID を作成することができます、 **新規 Guid** コマンドレット)。  **ConfigurationID** プル サーバー上の適切な構成を検索する、LCM の使用です。 プル サーバー上の構成の MOF ファイルを指定する必要があります `ConfigurationID.mof`, ここで、 *ConfigurationID* の値には、 **ConfigurationID** 対象のノードの LCM のプロパティです。




