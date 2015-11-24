#PowerShell の必要な状態の構成部分の構成

> 5.0 Windows PowerShell に適用されます。

PowerShell の 5.0 では、必要な状態 Configuration (DSC) はフラグメントでは複数のソースからの配信に構成を使用できます。 ターゲット ノード上のローカル構成マネージャー (LCM) は、1 つの構成として適用する前にまとめて、フラグメントを配置します。 この機能により、チームまたは個人の間の構成の制御を共有します。 たとえば場合は、サービスでは、次の 2 つまたは複数の開発者のチームが共同作業、各作成することも、サービスの一部を管理するための構成。 プルの別のサーバーからプルされ、これらの構成の可能性があり、開発のさまざまな段階でそれらを追加することができます。 部分的な構成では、1 つの構成ドキュメントの編集を調整することがなく、ノードを構成するさまざまな側面を制御するには、さまざまな担当者やチームのこともできます。 たとえば、1 つのチーム別のチームが他のアプリケーションおよびその VM 上のサービス展開中に、VM とオペレーティング システムの展開を担当する場合があります。 部分的な構成では、各チームはされている、不必要に複雑なそれらのいずれかのことがなく、独自の構成を作成できます。

プッシュ モード、プル モード、または 2 つの組み合わせで部分の構成を使用することができます。

##プッシュ モードでの部分の構成

部分的な構成をプッシュ モードを使用するのには、一部の構成を受信するターゲット ノードで、LCM を構成します。 各部分の構成は、発行 DSCConfiguration コマンドレットを使用して、ターゲットにプッシュする必要があります。 ターゲット ノードがし、部分の構成には、1 つの構成を結合し、呼び出すことによって、構成を適用する、 [開始 DscConfigurationxt](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットです。

###部分の構成をプッシュ モード LCM を構成します。

作成する部分の構成の LCM をプッシュ モードを構成するのには **DSCLocalConfigurationManager** いずれかで構成 **PartialConfiguration** の各部分の構成ブロック。 LCM を構成する方法の詳細については、次を参照してください。 [Windows のローカルの Configuration Manager の構成](https://technet.microsoft.com/en-us/library/mt421188.aspx)です。 次の例では、2 つの部分的な構成が必要とする LCM 構成 —、OS を展開してする展開し、SharePoint を構成します。

```powershell
[DSCLocalConfigurationManager()]
configuration PartialConfigDemo
{
    Node localhost
    {

           PartialConfiguration OSInstall
        {
            Description = 'Configuration for the Base OS'
            RefreshMode = 'Push'
        }
           PartialConfiguration SharePointConfig
        {
            Description = 'Configuration for the SharePoint server'
            RefreshMode = 'Push'
        }
    }
}
PartialConfigDemo 
```

**RefreshMode** 各部分の構成を「プッシュ」に設定します。 名前、 **PartialConfiguration** (ここでは、"OSInstall"と"SharePointConfig") のブロックはターゲット ノードにプッシュするための構成の名前を正確に一致する必要があります。

###公開して、一部の構成をプッシュ モードの開始

![PartialConfig フォルダー構造](./images/PartialConfig1.jpg)

次に呼び出し、 **発行 DSCConfiguration** 構成ごとに、パスのパラメーターとして構成ドキュメントの格納フォルダーを渡します。 両方の構成を公開するには、後に呼び出すことができます `開始 DSCConfiguration – 既存` のターゲット ノード上。

##プル モードでの部分の構成

一部の構成は、1 つまたは複数のプル サーバーから取得できます (プル サーバーに関する詳細については、次を参照してください。 [Windows PowerShell 必要な状態の構成プル サーバー](pullServer.md)です。 これを行うには、ターゲット ノードが、一部の構成を取得し、名前をプル サーバーで正しく構成ドキュメントを見つけることで、LCM を構成する必要があります。

###プル ノードの構成の LCM を構成します。

プル サーバーからの部分的な構成をプルする LCM を構成するには、いずれかでプルのサーバーを定義する、 **ConfigurationRepositoryWeb** (HTTP プル サーバーの場合は) 用または **ConfigurationRepositoryShare** (SMB プル サーバー) をブロックします。 次に、作成 **PartialConfiguration** ブロックを使用して、プル サーバーを参照している、 **ConfigurationSource** プロパティです。 また、LCM がプル モードを使用することを指定してプルのサーバーとリンク先ノードが、構成を識別するために使用する ConfigurationID を指定する設定のブロックを作成する必要があります。 次のメタ構成が CONTOSO PullSrv をという名前の HTTP プルのサーバーを定義し、2 つの部分的な構成を使用するサーバーを取得します。

```powershell
[DSCLocalConfigurationManager()]
configuration PartialConfigDemo
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

           PartialConfiguration OSInstall
        {
            Description = 'Configuration for the Base OS'
            ConfigurationSource = '[ConfigurationRepositoryWeb]CONTOSO-PullSrv'
            RefreshMode = 'Pull'
        }
           PartialConfiguration SharePointConfig
        {
            Description = 'Configuration for the Sharepoint Server'
            ConfigurationSource = '[ConfigurationRepositoryWeb]CONTOSO-PullSrv'
            DependsOn = [PartialConfiguration]OSInstall
            RefreshMode = 'Pull'
        }
    }
}
PartialConfigDemo 
```

プルの 2 つ以上のサーバーからの部分的な構成をプルすることができます: だけ必要になりますを各プルのサーバーを定義し、各 PartialConfiguration ブロックのプルを適切なサーバーを参照してください。

メタ構成を作成した後、構成のドキュメント (MOF ファイル) を作成し、[セット DscLocalConfigurationManager] (https://technet.microsoft.com/en-us/library/dn521621、LCM を構成するには、(v=wps.630).aspx) を呼び出すを実行する必要があります。

###名前付けとプルのサーバー上の構成ドキュメントを配置します。

構成の部分的なドキュメントは、として指定されたフォルダーに配置する必要があります、 **ConfigurationPath** で、 `web.config` プル サーバーのファイル (通常 `C:\Program Files\WindowsPowerShell\DscService\Configuration`)。 構成のドキュメントを次のように名前必要があります。 _ConfigurationName_です。 _ConfigurationID_`.mof`, ここで、 _ConfigurationName_ 部分の構成の名前を指定し、 _ConfigurationID_ は、ターゲット ノードで LCM で定義された構成の ID。 例では、構成のドキュメントは次のように名前をする必要があります。
![プル サーバー上の PartialConfig 名](images/PartialConfigPullServer.jpg)

###プル サーバーから部分的な構成を実行しています。

ターゲット ノードの電源は部分の構成を取得いること、結合、および一定の間隔で指定したとおりで、結果の構成を適用のターゲット ノード上の LCM が構成されているを後の構成ドキュメントを作成およびプルのサーバーで正しくという名前の **RefreshFrequencyMins** 、LCM のプロパティです。 強制的に更新する場合を構成を取得して、更新 DscConfiguration コマンドレットを呼び出すことができ、 `開始 DSCConfiguration – 既存` に適用するようにします。

##プッシュおよびプルの混在モードでの部分の構成

プッシュを混在させるし、部分的な構成のモードを取得できます。 つまり、他の部分の構成がプッシュされたときに、プル サーバーからを取得する 1 つの部分的な構成ができます。 によっては、前のセクションで説明するように、更新モードのと同様に、各部分の構成を処理します。 たとえば、次のメタ構成では、同じ例では、プル モードでオペレーティング システムの一部の構成とプッシュ モードで SharePoint の部分的な構成について説明します。

```powershell
[DSCLocalConfigurationManager()]
configuration PartialConfigDemo
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

           PartialConfiguration OSInstall
        {
            Description = 'Configuration for the Base OS'
            ConfigurationSource = '[ConfigurationRepositoryWeb]CONTOSO-PullSrv'
            RefreshMode = 'Pull'
        }
           PartialConfiguration SharePointConfig
        {
            Description = 'Configuration for the Sharepoint Server'
            DependsOn = [PartialConfiguration]OSInstall
            RefreshMode = 'Push'
        }
    }
}
PartialConfigDemo 
```

なお、 **RefreshMode** 「プル」は、設定のブロックで指定しますが、 **RefreshMode** 、OSInstall 部分の構成は「プッシュ」です。

名前をそれぞれの更新モードを上記のように構成ドキュメントを検索するとします。 呼び出します。 **発行 DSCConfiguration** 、SharePointInstall を発行すると部分的な構成では、待機するサーバーから取得した、プル、または、[更新 DscConfiguration] (https://technet.microsoft.com/en-us/library/mt143541 (v=wps.630).aspx) を呼び出すことによって強制的に更新するには、OSInstall 構成。

##関連項目

**概念**
[Windows PowerShell の必要なプル サーバーの状態の構成](pullServer.md) 
[Windows のローカルの Configuration Manager の構成](https://technet.microsoft.com/en-us/library/mt421188.aspx)




