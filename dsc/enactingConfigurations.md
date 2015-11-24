#構成を制定します。

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

PowerShell の必要な状態 Configuration (DSC) の構成を指定するのには 2 つの方法があります。 モードとプルのモードをプッシュします。

##プッシュ モード

![プッシュ モード](images/Push.png "How push mode works")

プッシュ モードがアクティブにターゲット ノードに呼び出すことによって、構成を適用するユーザーを指す、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットです。

作成し、構成をコンパイルするには、後にすることができます施行、プッシュ モードで呼び出すことによって、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) 設定のコマンドレットのコマンドレットでは、構成の MOF のパスを-path パラメーター。 たとえば、MOF の構成がで locted `C:\DSC\Configurations\localhost.mof`, 、次のコマンドを使用して、ローカル コンピューターに適用することは。
`開始 DscConfiguration-'C:\DSC\Configurations' のパス`

> __注__: 既定では、DSC はバック グラウンド ジョブとして、構成を実行します。 構成を対話的に実行するには、呼び出し、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) で、 __-待機__ パラメーター。

##プル モード

![プル モード](images/Pull.png "How pull mode works")
プル モードでは、リモートのプル サーバーからその構成を目的の状態を取得するプル クライアントは構成されます。 同様に、プル サーバーでは、ホスト、DSC サービスでは、するように設定されているしに、構成とプルのクライアントに必要なリソースが提供されてです。
それぞれのプル クライアントが、ノードの構成のコンプライアンス対応の定期的なチェックを実行する、スケジュールされたタスク。 イベントには、最初の時間が開始されるとは、構成を検証するプル クライアント上ローカルの Configuration Manager (LCM) が行われます。 としてプルのクライアントが構成されている場合は、必要に応じて、何も起こりません。 それ以外の場合、LCM は、特定の構成を取得するプルのサーバーへの要求を作成します。 その構成が、プルのサーバー上に存在し、最初の検証チェックにパスしたこと、構成は、LCM によって実行される、プル クライアントに送信されます。

次のトピックでは、プル サーバーとクライアントを設定する方法について説明します。

- [プルの web サーバーの設定](pullServer.md)
- [SMB のプル サーバーのセットアップ](pullServerSMB.md)
- [プル クライアントを構成します。](pullClientConfigID.md)



