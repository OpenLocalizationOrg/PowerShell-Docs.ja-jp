#DSC web プル サーバーのセットアップ

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

DSC web プル サーバーとは、OData のインターフェイスを使用して、これらのノードに表示するときのために DSC 構成ファイルをターゲット ノードを使用できるようにするための IIS での web サービスです。

プル サーバーを使用するための要件:

* 実行するサーバー。
   - WMF/PowerShell 4.0 以上
   - IIS サーバーの役割
   - DSC サービス
* 理想的には、いくつかは証明書の生成のローカル Configuration Manager (LCM) を対象のノードに渡される資格情報をセキュリティで保護するには

DSC サービスと IIS サーバーの役割を追加するには、サーバー マネージャーで、または PowerShell を使用して追加の役割と機能のウィザードを使用します。 このトピックで説明するサンプル スクリプトは処理の手順を両方のもします。

##XWebService リソースを使用します。

プルの web サーバーを設定する最も簡単な方法は xPSDesiredStateConfiguration モジュールに含まれる、xWebService リソースを使用することです。 次の手順では、web サービスを設定する構成で、リソースを使用する方法について説明します。

1. 呼び出す、 [インストール モジュール](https://technet.microsoft.com/en-us/library/dn807162.aspx) コマンドレットをインストールする、 **xPSDesiredStateConfiguration** モジュール。 **注**: **インストール モジュール** に含まれる、 **PowerShellGet** モジュールでは、PowerShell 5.0 に含まれています。 ダウンロードすることができます、 **PowerShellGet** PowerShell 3.0 と 4.0 のモジュール [また PowerShell モジュールのプレビュー](https://www.microsoft.com/en-us/download/details.aspx?id=49186)です。
1. サブジェクトに自己署名証明書を作成する `"CN = PSDSCPullServerCert"` で、 `CERT: \LocalMachine\MY\` を格納します。 コマンドを使用してこれを行う `'\LocalMachine\MY' を新規 SelfSignedCertificate CertStoreLocation-サブジェクト' CN = PSDSCPullServerCert'`です。
1. PowerShell ISE で、f5 キーを押してを開始します。 次の構成スクリプト (Sample_xDscWebService.ps1 として展開したファイルに含まれる)。 このスクリプトでは、プルのサーバーと準拠サーバーを設定します。

```powershell
Configuration Sample_xDSCService
{
    param (
    [ValidateNotNullOrEmpty()]
    [String] $certificateThumbPrint
    )
    Import-DSCResource –ModuleName DSCServic
    Node localhost
    {
        WindowsFeature DSCServiceFeature
        {
            Ensure = “Present”
            Name = “DSC-Service”
        }

        DSCService PSDSCPullServer
        {
            Ensure   = "Present"
            Name     = “PSDSCPullServer”
            Port     = 8080
            PhysicalPath = "$env:SystemDrive\inetpub\wwwroot\PSDSCPullServer"
            EnableFirewallException = $true
            CertificateThumbprint = $certificateThumbPrint
            ModulePath = “$env:PROGRAMFILES\WindowsPowerShell\DscService\Modules”
            ConfigurationPath = “$env:PROGRAMFILES\WindowsPowerShell\DscService\Configuration”
            State = “Started”
            DependsOn = “[WindowsFeature]DSCServiceFeature             
        }
    }
}
```

1. 構成を実行するには、自己署名の拇印を渡すことの証明書として作成した、 **certificateThumbPrint** パラメーター。

```powershell
PS:\>$myCert = Get-ChildItem CERT: | Where {$_.Subject -eq 'CN=PSDSCPullServerCert'}
PS:\>Sample_xDSCService -certificateThumbprint $myCert.Thumbprint 
```

##登録キー

クライアントの構成名、構成の ID は、登録キーの代わりに使用できるように、サーバーに登録するノードを許可する (は GUID です、サーバーとクライアント] ノードの両方に呼ばれる) という名前のファイルに配置する必要があります `RegistrationKeys.txt`です。 既定では、この例で作成されたプル サーバー内で検索するには、そのファイルが必要ですが `C:\Program Files\WindowsPowerShell\DscService`です。 登録キーで構成される行を 1 つだけでテキスト ファイルを作成し、そのフォルダーに保存します。
> **注**: PowerShell 4.0 では、登録キーはサポートされていません。

##構成とリソースを配置します。

下に新しいフォルダーがある、プル server のセットアップが完了したら、 `$env:path: PROGRAMFILES\WindowsPowerShell` という名前の"DscService"です。 そのフォルダーには、「モジュール」および「構成」という名前の 2 つのフォルダーがあります。 「モジュール」フォルダーには、このサーバーからノードを取得する構成が必要とするすべてのリソースを配置します。 [構成] フォルダー内のノードによってプルされるは構成のいずれかの構成の MOF ファイルを配置します。 MOF ファイルの名前は、プルのクライアントの種類によって異なります。 次のトピックでは、詳細にプル クライアントの設定について説明します。

* [DSC プルを構成の ID を使用してクライアントの設定](pullClientConfigID.md)
* [DSC プルを構成名を使用してクライアントの設定](pullClientConfigNames.md)
* [一部の構成](partialConfigs.md)

##参照してください。

* [Windows PowerShell の必要な状態の構成の概要](overview.md)
* [構成を制定します。](enactingConfigurations.md)
* [DSC プル サーバーからノード情報を取得する方法](retrieveNodeInfo.md)



