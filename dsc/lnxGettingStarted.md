#Linux に必要な状態 Configuration (DSC) を開始します。

このトピックでは、Linux 用の PowerShell の必要な状態 Configuration (DSC) を使用して開始する方法について説明します。 DSC に関する一般的な情報は、次を参照してください。 [Windows PowerShell 必要な状態の構成を使ってみる](overview.md)です。

##サポートされている Linux 操作システムのバージョン

次のバージョンの Linux オペレーティング システムは、Linux 用 DSC はサポートします。
- CentOS 5、6、および 7 (x86 と x64)
- Debian Gnu/linux 6 と 7 (x86 と x64)
- Oracle Linux 5、6 および 7 (x86 と x64)
- Red Hat Enterprise Linux Server 5、6 および 7 (x86 と x64)
- 10、11、12 (x86 と x64)、SUSE Linux Enterprise Server
- Ubuntu Server 12.04 LTS および 14.04 LTS (x86 と x64)

次の表には、Linux 用の DSC ackage の必要な依存関係がについて説明します。

| 必要なパッケージ| 説明| 最小バージョン|
|---|---|---|
| glibc| GNU ライブラリ| 2…4 – 31.30|
| python| Python| 2.4 – 3.4|
| omiserver| インフラストラクチャの管理] を開きます| 1.0.8.1|
| openssl| OpenSSL ライブラリ| 0.9.8 または 1.0|
| ctypes| Python CTypes ライブラリ| Python バージョンに一致する必要があります。|
| libcurl| cURL の http クライアント ライブラリ| 7.15.1|

##Linux 用 DSC をインストールします。

インストールする必要があります、 [Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/) linux DSC をインストールする前にします。

###OMI をインストールします。

Linux の必要な状態の構成では、バージョン 1.0.8.1、Open Management Infrastructure (OMI) CIM サーバーが必要です。 OMI は、Open Group からダウンロードできます: [Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/)です。

OMI をインストールするには、Linux システムでも優れています (.deb) と OpenSSL のバージョンに適したパッケージをインストールします (ssl_098 または ssl_100)、およびアーキテクチャ (x64 または x86)。 RPM パッケージは、CentOS、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、および Oracle Linux の操作に適しています。 DEB パッケージは、Debian Gnu/linux および Ubuntu Server の操作に適しています。 Ssl_098 のパッケージは、ssl の中にインストールされている 0.9.8 の OpenSSL を使用してコンピューターの適切な_100 のパッケージは、OpenSSL 1.0 がインストールされているとのコンピューターに適切です。

> **注**: インストールされている OpenSSL のバージョンを確認するには、コマンドを実行 `openssl のバージョン`です。

CentOS 7 x64 システム上の OMI をインストールするには、次のコマンドを実行します。

`# sudo rpm か omiserver 1.0.8.ssl_100.rpm`

###DSC をインストールします。

DSC をインストールするには、Linux システムでも優れています (.deb) と OpenSSL のバージョンに適したパッケージをインストールします (ssl_098 または ssl_100)、およびアーキテクチャ (x64 または x86)。 RPM パッケージは、CentOS、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、および Oracle Linux の操作に適しています。 DEB パッケージは、Debian Gnu/linux および Ubuntu Server の操作に適しています。 Ssl_098 のパッケージは、ssl の中にインストールされている 0.9.8 の OpenSSL を使用してコンピューターの適切な_100 のパッケージは、OpenSSL 1.0 がインストールされているとのコンピューターに適切です。

> **注**: インストールされている OpenSSL のバージョンを確認するにコマンド openssl のバージョンを実行します。

DSC を CentOS 7 x64 システムにインストールするには、次のコマンドを実行します。

`# sudo rpm か dsc-1.0.0-254.ssl_100.x64.rpm`


##DSC を使用して、Linux 用

次のセクションでは、作成、および Linux コンピューターで DSC 構成を実行する方法について説明します。

###構成の MOF ドキュメントを作成します。

Windows PowerShell の構成のキーワードを使用して、Windows コンピューターのと同じように、Linux、コンピューターの構成を作成します。 次の手順では、Windows PowerShell を使用して Linux コンピューターの構成ドキュメントの作成について説明します。

1. Nx モジュールをインポートします。 Nx の Windows PowerShell モジュールは DSC の linux の場合、スキーマ組み込みのリソースにはが含まれていますと、ローカル コンピューターにインストールされているおよび構成でインポートする必要があります。
   
   -Nx モジュールをインストールするには nx モジュール ディレクトリをコピーするか、 `%UserProfile%\Documents\WindowsPowerShell\Modules\` または `C:\windows\system32\WindowsPowerShell\v1.0\Modules`です。 For Linux のインストール パッケージ (MSI) DSC には nx モジュールが含まれます。 構成で nx モジュールをインポートするには、使用、 __インポート DSCResource__ コマンド。

```powershell
Configuration ExampleConfiguration{

    Import-DSCResource -Module nx

}
```
2. 構成を定義し、構成ドキュメントを生成します。

```powershell
Configuration ExampleConfiguration{

    Import-DSCResource -Module nx

    Node  "linuxhost.contoso.com"{
    nxFile ExampleFile {

        DestinationPath = "/tmp/example"
        Contents = "hello world `n"
        Ensure = "Present"
        Type = "File"
    }

    }
}
ExampleConfiguration -OutputPath:"C:\temp" 
```

###Linux コンピューターに、構成をプッシュします。

(MOF ファイル) の構成に関するドキュメントを使用して Linux コンピューターにプッシュできます、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットです。 と共に、このコマンドレットを使用するのには、 [Get DscConfiguration](https://technet.microsoft.com/en-us/library/dn407379)、.aspx または [テスト DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) コマンドレット、リモートでするには、Linux コンピューターでは、使用、CIMSession します。  [New-cimsession](https://technet.microsoft.com/en-us/library/jj590760.aspx) コマンドレットは、Linux コンピューターへの CIMSession を作成するために使用します。

次のコードでは、Linux 用の DSC、CIMSession を作成する方法を示します。

```powershell
$Node = "ostc-dsc-01"
$Credential = Get-Credential -UserName:"root" -Message:"Enter Password:"

#Ignore SSL certificate validation
#$opt = New-CimSessionOption -UseSsl:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

#Options for a trusted SSL certificate
$opt = New-CimSessionOption -UseSsl:$true 
$Sess=New-CimSession -Credential:$credential -ComputerName:$Node -Port:5986 -Authentication:basic -SessionOption:$opt -OperationTimeoutSec:90 
```

> **Note**:
* 「プッシュ」モードでは、ユーザーの資格情報は、Linux コンピューターの root ユーザーである必要があります。
* SSL や TLS 接続のみはサポートされて DSC – UseSSL パラメーターを $true に設定は、Linux、New-cimsession を使用する必要があります。
* OMI (DSC) を使用する SSL 証明書は、ファイルで指定します。 `/opt/omi/etc/omiserver.conf` のプロパティを持つ: pemfile およびキー ファイルです。
   実行している Windows コンピューターによっては、この証明書は信頼されていない場合、 [New-cimsession](https://technet.microsoft.com/en-us/library/jj590760.aspx) コマンドレット、CIMSession オプションを使用して証明書の検証を無視する選択できます `- SkipCACheck: $true サーバー: $true SkipRevocationCheck: $true`。

Linux のノードに DSC 構成をプッシュするには、次のコマンドを実行します。

`開始 DSCConfiguration-パス:"C:\temp"cimsession: $sess-待機 - 詳細`

###プル サーバーを使用して構成を配布します。

同じようには Windows コンピューターの構成をプルのサーバーと Linux コンピューターに配布できます。 プル サーバーの使用方法の詳細については、次を参照してください。 [Windows PowerShell 必要な状態の構成プル サーバー](pullServer.md)です。 追加情報とプルのサーバーでの Linux コンピューターの使用に関する制限事項は、Linux の状態の必要な構成のリリース ノートを参照します。

###構成をローカルでの操作

DSC Linux 用には、ローカルの Linux コンピューターから構成を使用するスクリプトが含まれています。 これらのスクリプト内で検索が `/opt/microsoft/dsc/Scripts` し、次に示します。
* GetConfiguration.py
   
   コンピューターに適用される現在の構成を返します。 Windows PowerShell のコマンドレット Get DscConfiguration コマンドレットと似ています。

`# sudo./GetConfiguration.py`

* GetMetaConfiguration.py
   
   コンピューターに適用される現在メタ構成を返します。 このコマンドレットのような [Get DSCLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx) コマンドレットです。

`# sudo./GetMetaConfiguration.py`

* InstallModule.py
   
   カスタム DSC リソース モジュールをインストールします。 モジュールの共有オブジェクト ライブラリを含む .zip ファイルとスキーマの MOF ファイルへのパスが必要です。

`# sudo./InstallModule.py/tmp/cnx_Resource.zip`

* RemoveModule.py
   
   カスタム DSC リソース モジュールを削除します。 削除するのには、モジュールの名前が必要です。

`# sudo./RemoveModule.py cnx_Resource`

* SendConfigurationApply.py
   
   構成の MOF ファイルをコンピューターに適用します。 ような [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットです。 構成を適用する MOF のパスが必要です。

`# sudo./RemoveModule.py cnx_Resource`

* SendMetaConfiguration.py
   
   メタ構成の MOF ファイルをコンピューターに適用します。 ような [セット DSCLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn521621.aspx) コマンドレットです。 適用するメタ構成の MOF のパスが必要です。

`# sudo./SendMetaConfiguration.py – configurationmof/tmp/localhost.meta.mof`

##PowerShell の必要な Linux ログ ファイルの状態の構成

DSC は、Linux のメッセージの次のログ ファイルが生成されます。

| ログ ファイル| Directory| 説明|
|---|---|---|
| omiserver.log| /選択/omi/var/ログ/| OMI CIM サーバーの操作に関連するメッセージ。|
| dsc.log| /選択/omi/var/ログ/| ローカル構成マネージャー (LCM) と DSC リソースの操作の操作に関連するメッセージ。|




