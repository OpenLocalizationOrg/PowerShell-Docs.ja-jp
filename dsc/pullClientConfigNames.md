#構成名を使用してクライアントをプルの設定

> 5.0 Windows PowerShell に適用されます。

対象の各ノードでは、プル モードを使用するように指示して、構成を取得するプルのサーバーと通信できる URL を指定します。 これを行うには、必要な情報を使用してローカルの Configuration Manager (LCM) を構成する必要があります。LCM を構成するには、特殊な種類で修飾されている構成の作成、 **DSCLocalConfigurationManager** 属性です。 LCM を構成する方法の詳細については、次を参照してください。 [ローカルの Configuration Manager の構成](metaConfig.md)です。

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
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }
        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
            ConfigurationNames = @('ClientConfig')

        }      
    }
}
PullClientConfigID
```

スクリプトでは、 **ConfigurationRepositoryWeb** ブロックは、プル サーバーを定義します。  **よう** プロパティ プル サーバーのエンドポイントを指定します。

**RegistrationKey** プロパティがプル サーバーのすべてのクライアント ノードとそのプル サーバー間で共有キー。 同じ値は、プル サーバー上のファイルに格納されます。  **ConfigurationNames** プロパティは、[クライアント] ノード用の構成の名前を指定します。 プル サーバーで、構成の MOF ファイルこのクライアント ノードを指定する必要があります *ConfigurationNames*.mof、ここで *ConfigurationNames* の値と一致する、 **ConfigurationNames** この metaconfiguration で設定するプロパティです。

という名前の新しい出力フォルダーを作成した後、このスクリプトが実行される、 **PullClientConfigID** あります metaconfiguration の MOF ファイルを格納します。 Metaconfiguration MOF ファイルをという名前がここでは、 `localhost.meta.mof`です。

構成を適用するには、呼び出し、 **セット DscLocalConfigurationManager** コマンドレットで、 **パス** metaconfiguration の MOF ファイルの場所に設定します。 例: `セット DSCLocalConfigurationManager localhost – パス \PullClientConfigID – Verbose。`。

> **注**。 登録キーは、プルの web サーバーでのみ機能します。 使用する必要があります **ConfigurationID** SMB プル サーバーとします。 使用して、プル サーバーを構成する方法について **ConfigurationID**, を参照してください [プル クライアントを構成の ID を使用してセットアップ](pullClientConfigID.md)

##リソースとレポート サーバー

既定では、[クライアント] ノードは、構成のプル サーバーへから必要なリソースとレポートの状態を取得します。 ただし、リソース、およびレポート サーバーを別のプルを指定できます。
いずれかのリソース サーバーを指定するには、使用する、 **ResourceRepositoryWeb** (用 web プル サーバーの場合) または **ResourceRepositoryShare** ブロック (SMB プル サーバーの場合)。
使用するレポート サーバーを指定する、 **ReportRepositoryWeb** ブロックします。 レポート サーバーは、SMB サーバーにすることはできません。
次 metaconfiguration からには、その構成を取得することをプルのクライアントを構成する **CONTOSO PullSrv** とそのリソースから **CONTOSO ResourceSrv**, とに、状態レポートを送信する **CONTOSO ReportSrv**:

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded = $true
        }

        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = 'fbc6ef09-ad98-4aad-a062-92b0e0327562'
        }

        ResourceRepositoryWeb CONTOSO-ResourceSrv
        {
            ServerURL = 'https://CONTOSO-REsourceSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '30ef9bd8-9acf-4e01-8374-4dc35710fc90'
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

* [構成の ID を使用するプル クライアントの設定](pullClientConfigID.md)
* [DSC web プル サーバーのセットアップ](pullServer.md)




