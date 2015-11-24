#構成の ID を使用してプル クライアントを設定します。

> 5.0 Windows PowerShell に適用されます。

対象の各ノードでは、プル モードを使用するように指示して、構成を取得するプルのサーバーと通信できる URL を指定します。 これを行うには、必要な情報を使用してローカルの Configuration Manager (LCM) を構成する必要があります。 LCM を構成するには、特殊な種類の場合は 300 の構成の作成、 **DSCLocalConfigurationManager** 属性です。 LCM を構成する方法の詳細については、次を参照してください。 [ローカルの Configuration Manager の構成](metaConfig.md)です。

> **注**: このトピックは、PowerShell 5.0 に適用されます。 PowerShell 4.0 でプルのクライアントをセットアップする方法については、次を参照してください [プルを PowerShell 4.0 での構成の ID を使用してクライアントを設定](pullClientConfigID4.md)。

次のスクリプトでは、"CONTOSO PullSrv"をという名前のサーバーからプルの構成に、LCM を構成します。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            ConfigurationID = 1d545e3b-60c3-47a0-bf65-5afc05182fd0'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }
        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'

        }      
    }
}
PullClientConfigID
```

スクリプトでは、 **ConfigurationRepositoryWeb** ブロックは、プル サーバーを定義します。  **よう**

という名前の新しい出力フォルダーを作成した後、このスクリプトが実行される、 **PullClientConfigID** あります metaconfiguration の MOF ファイルを格納します。 Metaconfiguration MOF ファイルをという名前がここでは、 `localhost.meta.mof`です。

構成を適用するには、呼び出し、 **セット DscLocalConfigurationManager** コマンドレットで、 **パス** metaconfiguration の MOF ファイルの場所に設定します。 例: `セット DSCLocalConfigurationManager localhost – パス \PullClientConfigID – Verbose。`。

##構成の ID

スクリプトのセット、 **ConfigurationID** LCM をこの目的のために以前作成した GUID のプロパティ (を使用して GUID を作成することができます、 **新規 Guid** コマンドレット)。  **ConfigurationID** プル サーバー上の適切な構成を検索する、LCM の使用です。 プル サーバー上の構成の MOF ファイルを指定する必要があります _ConfigurationID_.mof、ここで _ConfigurationID_ の値には、 **ConfigurationID** 対象のノードの LCM のプロパティです。

##SMB プル サーバー

SMB サーバーからクライアントをプルの構成を設定するには、使用、 **ConfigurationRepositoryShare** ブロックします。  **ConfigurationRepositoryShare** ブロックを設定して、サーバーへのパスを指定する、 **SourcePath** プロパティです。 次の metaconfiguration から SMB プル サーバーという名前を取得する対象のノードを構成する **SMBPullServer**です。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            ConfigurationID = '1d545e3b-60c3-47a0-bf65-5afc05182fd0'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }
        ConfigurationRepositoryWeb SMBPullServer
        {
            SourcePath = '\\SMBPullServer\PullSource'

        }     
    }
}
PullClientConfigID
```

##リソースとレポート サーバー

既定では、[クライアント] ノードは、構成のプル サーバーへから必要なリソースとレポートの状態を取得します。 ただし、リソース、およびレポート サーバーを別のプルを指定できます。
いずれかのリソース サーバーを指定するには、使用する、 **ResourceRepositoryWeb** (用 web プル サーバーの場合) または **ResourceRepositoryShare** ブロック (SMB プル サーバーの場合)。
使用するレポート サーバーを指定する、 **ReportRepositoryWeb** ブロックします。 レポート サーバーは、SMB サーバーにすることはできません。
次 metaconfiguration からには、その構成を取得することをプルのクライアントを構成する **CONTOSO PullSrv** とそのリソースから **CONTOSO ResourceSrv**, とに、状態レポートを送信する **CONTOSO ReportSrv**です。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            ConfigurationID = '1d545e3b-60c3-47a0-bf65-5afc05182fd0'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'

        }

        ResourceRepositoryWeb CONTOSO-ResourceSrv
        {
            ServerURL = 'https://CONTOSO-REsourceSrv:8080/PSDSCPullServer.svc'
        }

        ReportServerWeb CONTOSO-ReportSrv
        {
            ServerURL = 'https://CONTOSO-REsourceSrv:8080/PSDSCPullServer.svc'
        }
    }
}
PullClientConfigID
```

##関連項目

* [構成名を使用するプル クライアントの設定](pullClientConfigNames.md)



