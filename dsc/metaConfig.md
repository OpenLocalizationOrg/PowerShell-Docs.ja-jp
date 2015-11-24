#ローカルの Configuration Manager の構成

> 5.0 Windows PowerShell に適用されます。

ローカル構成マネージャー (LCM) は、エンジンの Windows PowerShell 必要な状態 Configuration (DSC) です。 LCM では、すべてのターゲット ノードで実行され、解析と、ノードに送信される構成の施行を担当します。 いくつかの DSC、以下の他の側面を担当します。

* 更新モード (プッシュまたはプル) を決定します。
* ノードがどのくらいの頻度をプルし、構成の施行を指定します。
* プル サーバーと、ノードを関連付けます。
* 部分的な構成を指定します。

特殊な種類の構成を使用すると、これらの動作のそれぞれを指定するのに LCM を構成できます。 次のセクションでは、LCM を構成する方法について説明します。

> **注**: このトピックは、Windows PowerShell 5.0 で導入された LCM に適用されます。 Windows PowerShell 4.0 で、LCM を構成する方法の詳細については、Windows PowerShell 4.0 必要な状態構成ローカル構成マネージャーを参照してください。

##作成と、LCM 構成を制定します。

LCM を構成するのには、作成し、特殊な種類の構成を実行します。 LCM は、構成を指定するには、DscLocalConfigurationManager 属性を使用します。 LCM をプッシュ モードに設定する単純な構成を次に示します。

```powershell
[DSCLocalConfigurationManager()]
configuration LCMConfig
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Push'
        }
    }
} 
```

呼び出すし、標準的な構成と同様に、MOF の構成を作成する構成を実行する (MOF の構成を作成する方法の詳細についてを参照して Windows PowerShell 必要な状態の構成を使ってみる)。 通常の構成とは異なりするはいない施行 LCM の構成が呼び出すことによって、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットです。 代わりに、パラメーターとして MOF の構成へのパスを指定してセット DscLocalConfigurationManager コマンドレットを呼び出します。 呼び出して、LCM のプロパティを表示することができます、構成を適用した後、 [Get DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx) コマンドレットです。

LCM は、構成では、限定されたリソースのセットに対してのみのブロックを含めることができます。 呼ばれる唯一のリソースは、前の例では、 **設定**です。 使用できるその他のリソースは次のとおりです。

* **ConfigurationRepositoryWeb**: HTTP プル サーバーの構成を指定します。
* **ConfigurationRepositoryShare**: SMB プル サーバーの構成を指定します。
* **ResourceRepositoryWeb**: HTTP モジュールのプル サーバーを指定します。
* **ResourceRepositoryShare**: モジュールの SMB プル サーバーを指定します。
* **ReportServerWeb**: レポートを送信する HTTP プル サーバーを指定します。
* **PartialConfiguration**: 部分的な構成を指定します。

##基本設定

構成されたすべてのプロパティ、LCM のプル サーバーと一部の構成を指定する以外、 **設定** ブロックします。 次のプロパティはで使用できる、 **設定** ブロックします。

| プロパティ| 種類| 説明|
|--- |--- |---|
| ConfigurationModeFrequencyMins| UInt32| 頻度を分単位で、現在の構成がチェックされ適用。ApplyOnly を ConfigurationMode プロパティが設定されている場合、このプロパティは無視されます。既定値は、15 です。__注__。 このプロパティの値のいずれかの値の倍数でなければなりません、 __RefreshFrequencyMins__ プロパティ、またはの値、 __RefreshFrequencyMins__ プロパティは、このプロパティの値の倍数を指定する必要があります。|
| RebootNodeIfNeeded| bool| これを設定 __$true__ 自動的に再起動の適用が必要な構成した後に、ノードを再起動します。それ以外の場合、それを必要とする任意の構成にノードを手動で再起動する必要があります。既定値は __$false__です。|
| ConfigurationMode| string| 方法、LCM 実際に適用される、構成対象のノードを指定します。次の値がかかることができます: __"ApplyOnly"__: DSC は構成を適用し、ターゲット ノードに、または新しい構成がサーバーから取得したときに、新しい構成がプッシュされた場合を除きはさらに何もしません。最初のアプリケーションの新しい構成では、後に DSC をチェックしません以前に構成した状態からの誤差を列挙します。__"ApplyAndMonitor"__。 これは、既定値です。LCM には、新しい構成のいずれかが適用されます。最初のアプリケーションの新しい構成では後から目的の状態では、ターゲット ノードがしまいます DSC レポート ログの不一致 __"ApplyAndAutoCorrect"__: DSC に新しい構成のいずれかが適用されます。最初のアプリケーションの新しい構成では後から目的の状態では、ターゲット ノードがしまう場合、DSC がのログに不一致が報告し、現在の構成を再適用します。|
| ActionAfterReboot| string| 構成の適用中に、再起動後の動作を指定します。有効な値は次のように、: __"ContuinueConfiguration"__: 現在の構成の適用を続行__"。StopConfiguraiton"__: 現在の構成を停止します。|
| RefreshMode| string| LCM が構成を取得する方法を指定します。値には次のように。 __"Disabled"__。 このノードの DSC 構成が無効になっています。__「プッシュ」__: 開始 DscConfiguration コマンドレットを呼び出すことによって構成を開始します。構成は、ノードにすぐに適用されます。これは、既定値です。__プル:__ プル サーバーからの構成を定期的にチェックするノードを構成します。このプロパティがプルに設定されている場合は、プルのサーバーでを指定する必要があります、 __ConfigurationRepositoryWeb__ または __ConfigurationRepositoryShare__ ブロックします。プルのサーバーの詳細については、次を参照してください。 [DSC プル サーバーをセットアップする](pullServer.md)です。|
| 証明書 Id| string| 構成にアクセスするための資格情報をセキュリティで保護するために使用する証明書を指定する GUID です。詳細については、次を参照してください。 [Windows PowerShell 必要な状態の設定での資格情報をセキュリティで保護する](http://blogs.msdn.com/b/powershell/archive/2014/01/31/want-to-secure-credentials-in-windows-powershell-desired-state-configuration.aspx)ですか。|
| ConfigurationID| string| プル モードでのプル サーバーから取得する構成ファイルを識別する GUID です。ノードがプルされます MOF の構成の名前が ConfigurationID.mof をという名前の場合、プル上の構成がサーバーです。__注:__ このプロパティを設定する場合は、プル サーバーと、ノードを使用して登録する __RegistryKeys__ は機能しません。詳細については、次を参照してください。 [構成名を使用するプル クライアント セットアップ](pullClientConfigNames.md)です。|
| RefreshFrequencyMins| Uint32| 時間の間隔 (分) を LCM が更新された構成を取得するには、プル サーバーを確認します。この値には、プル モードで、LCM が構成されていない場合は無視されます。既定値は、30 です。__注:__  このプロパティの値のいずれかの値の倍数でなければなりません、 __ConfigurationModeFrequencyMins__ プロパティ、またはの値、 __ConfigurationModeFrequencyMins__ プロパティは、このプロパティの値の倍数を指定する必要があります。|
| AllowModlueOverwrite| bool| __$TRUE__ 構成のサーバーからダウンロードする新しい構成がターゲット ノード上の古いファイルを上書きできるようにするかどうか。以外の場合は、$FALSE です。|
| DebugMode| bool| 場合に設定 __$TRUE__, 、既にキャッシュされている場合でもこれにより、すべての DSC リソースを再読み込みする LCM です。キャッシュされたリソースを使用するの $FALSE に設定します。このプロパティを設定する通常 __$TRUE__ のリソースのデバッグ中に、 __$FALSE__ 実稼働用です。既定値は __$FALSE__です。|
| ConfigurationDownloadManagers| CimInstance| 廃止されたとします。使用して __ConfigurationRepositoryWeb__ と __ConfigurationRepositoryShare__ プル サーバーの構成を定義するブロック。|
| ResourceModuleManagers| CimInstance| 廃止されたとします。使用して __ResourceRepositoryWeb__ と __ResourceRepositoryShare__ ブロックをリソースを定義するのには、サーバーを取得します。|
| ReportManagers| CimInstance| 廃止されたとします。使用して __ReportServerWeb__ ブロックをレポートを定義するのには、サーバーを取得します。|
| PartialConfigurations| CimInstance| 実装されていません。使用しません。|
| StatusRetentionTimeInDays| UInt32| LCM、現在の構成の状態を保持する日数を指定します。|

##サーバーを取得します。

プル サーバーは、OData web サービスまたは DSC ファイルの中央の場所として使用される SMB 共有のいずれかです。 LCM 構成では、プル サーバーの次の種類の定義がサポートされています。

* **構成サーバー**: DSC 構成を格納するリポジトリです。 定義する構成のサーバーを使用して **ConfigurationRepositoryWeb** (web ベースのサーバーの) および **ConfigurationRepositoryShare** (SMB ベースのサーバーの) ブロックします。
* リソース サーバー-PowerShell モジュールとしてパッケージ化、DSC リソースを格納するリポジトリです。 定義するリソースがサーバーを使用して **ResourceRepositoryWeb** (web ベースのサーバーの) および **ResourceRepositoryShare** (SMB ベースのサーバーの) ブロックします。
* レポート サーバー: DSC は、レポートのデータを送信するサービスです。 使用してレポート サーバーを定義する **ReportServerWeb** ブロックします。 レポート サーバーは、web サービスである必要があります。

セットアップとプルのサーバーを使用する方法については、次を参照してください。 [DSC プル サーバーをセットアップする](pullServer.md)です。

##構成のサーバーのブロック

Web ベースの構成を定義するサーバーは、作成、 **ConfigurationRepositoryWeb** ブロックします。 A **ConfigurationRepositoryWeb** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| AllowUnsecureConnection| bool| 設定 **$TRUE** 認証なしでサーバーに、ノードからの接続を許可します。設定 **$FALSE** 認証を要求します。|
| 証明書 Id| string| サーバーに対する認証に使用する証明書を表す GUID です。|
| ConfigurationNames| String[]| ターゲット ノードによってプルされる構成の名前の配列です。使用して、ノードがプル サーバーに登録されている場合にのみ、これらは使用、 **RegistrationKey**です。詳細については、次を参照してください。 [構成名を使用するプル クライアント セットアップ](pullClientConfigNames.md)です。|
| RegistrationKey| string| プルのサーバーに、ノードを登録するための GUID です。詳細については、次を参照してください。 [構成名を使用するプル クライアント セットアップ](pullClientConfigNames.md)です。|
| よう| string| 構成のサーバーの URL。|

作成する、SMB ベースの構成のサーバーを定義するのには **ConfigurationRepositoryShare** ブロックします。 A **ConfigurationRepositoryShare** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| Credential| MSFT_Credential| SMB 共有に対する認証に使用する資格情報です。|
| SourcePath| string| SMB 共有のパス。|

##リソースのサーバーのブロック

Web ベースのリソースを定義するサーバーは、作成、 **ResourceRepositoryWeb** ブロックします。 A **ResourceRepositoryWeb** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| AllowUnsecureConnection| bool| 設定 **$TRUE** 認証なしでサーバーに、ノードからの接続を許可します。設定 **$FALSE** 認証を要求します。|
| 証明書 Id| string| サーバーに対する認証に使用する証明書を表す GUID です。|
| RegistrationKey| string| プルのサーバーにノードを識別する GUID です。詳細については、ノードを DSC プルのサーバーに登録する方法を参照してください。|
| よう| string| 構成のサーバーの URL。|

作成する、リソースの SMB ベースのサーバーを定義するのには **ResourceRepositoryShare** ブロックします。 **ResourceRepositoryShare** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| Credential| MSFT_Credential| SMB 共有に対する認証に使用する資格情報です。|
| SourcePath| string| SMB 共有のパス。|

##レポート サーバーのブロック

レポート サーバーは、OData web サービスである必要があります。 作成するレポート サーバーを定義するのには **ReportServerWeb** ブロックします。 **ReportServerWeb** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| AllowUnsecureConnection| bool| 設定 **$TRUE** 認証なしでサーバーに、ノードからの接続を許可します。設定 **$FALSE** 認証を要求します。|
| 証明書 Id| string| サーバーに対する認証に使用する証明書を表す GUID です。|
| RegistrationKey| string| プルのサーバーにノードを識別する GUID です。詳細については、ノードを DSC プルのサーバーに登録する方法を参照してください。|
| よう| string| 構成のサーバーの URL。|

##一部の構成

部分的な構成を定義するには、作成する、 **PartialConfiguration** ブロックします。 部分的な構成の詳細については、次を参照してください。 [DSC 部分構成](partialConfigs.md)です。 **PartialConfiguration** 次のプロパティを定義します。

| プロパティ| 種類| 説明|
|---|---|---|
| ConfigurationSource| string[]| 定義した、構成のサーバーの名前の配列 **ConfiguratoinRepositoryWeb** と **ConfigurationRepositoryShare** ブロックでは、部分的な構成がから取得されます。|
| DependsOn| 文字列の {}| この部分の構成を適用する前に完了する必要がありますが、その他の構成の名前の一覧。|
| 説明| string| 一部の構成について説明するために使用するテキストです。|
| ExclusiveResources| string[]| この部分の構成に排他的なリソースの配列です。|
| RefreshMode| string| ドメイン コント ローラーがこの部分の構成を取得する方法を指定するには.値は、次のように: **無効になっている**。 この部分の構成が無効になっています。**プッシュ**: 部分の構成が呼び出すことによって、ノードにプッシュ、 [発行 DscConfiguration](https://technet.microsoft.com/en-us/library/mt517875.aspx) コマンドレットです。呼び出して、構成を開始できます、ノードのすべての部分の構成は、プッシュまたは、サーバーから取得した後に `開始 DscConfiguration – 既存`です。これは、既定値です。**プル**: プル サーバーから部分的な構成を定期的にチェックするノードを構成します。このプロパティが「プル」に設定されている場合は、設定して、プル サーバーを指定する必要があります、 **ConfigurationSource** プロパティです。プルのサーバーの詳細については、次を参照してください。 [DSC プル サーバーをセットアップする](pullServer.md)です。|
| ResourceModlueSource| string[]| この部分の構成の必要なリソースのダウンロード元のリソース サーバーの名前の配列です。これらの名前は必要がありますで以前に定義されているリソース サーバーを参照してください。 **ResourceRepositoryWeb** と **ResourceRepositoryShare** ブロックします。|

##関連項目

###概念

Windows PowerShell の必要な状態構成を使ってみる 
[DSC プル サーバーのセットアップ](pullServer.md) 
[Windows PowerShell 4.0 に必要な構成ローカル Configuration Manager の状態](metaConfig4.md)

###その他のリソース

[セット DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn521621.aspx) 
[構成名を使用するプル クライアントの設定](pullClientConfigNames.md)




