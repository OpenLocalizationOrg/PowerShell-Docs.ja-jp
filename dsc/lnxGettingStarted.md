---
title: "Linux 用 Desired State Configuration (DSC) の概要"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 2283e797275f426b624119bd1191e58080780c09

---

# Linux 用 Desired State Configuration (DSC) の概要

このトピックでは、Linux 用 PowerShell Desired State Configuration (DSC) の使用を開始する方法について説明します。 DSC に関する一般的な情報については、「[Windows PowerShell Desired State Configuration の概要](overview.md)」を参照してください。

## サポートされている Linux オペレーティング システム バージョン

Linux 用 DSC では、次の Linux オペレーティング システム バージョンがサポートされています。
- CentOS 5、6、および 7 (x86/x64)
- Debian GNU/Linux 6 および 7 (x86/x64)
- Oracle Linux 5、6 および 7 (x86/x64)
- Red Hat Enterprise Linux Server 5、6 および 7 (x86/x64)
- SUSE Linux Enterprise Server 10、11、および 12 (x86/x64)
- Ubuntu Server 12.04 LTS および 14.04 LTS (x86/x64)

次の表では、Linux 用 DSC で必要なパッケージの依存関係について説明します。

|  必須パッケージ |  説明 |  最小バージョン | 
|---|---|---|
| glibc| GNU ライブラリ| 2...4 - 31.30| 
| python| Python| 2.4 - 3.4| 
| omiserver| Open Management Infrastructure (オープン管理インフラストラクチャ)| 1.0.8.1| 
| openssl| OpenSSL ライブラリ| 0.9.8 または 1.0| 
| ctypes| Python CTypes ライブラリ| Python のバージョンに一致する必要があります。| 
| libcurl| cURL http クライアント ライブラリ| 7.15.1| 

## Linux 用 DSC のインストール

Linux 用 DSC をインストールする前に、[Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/) をインストールする必要があります。

### OMI のインストール

Linux 用 Desired State Configuration には、Open Management Infrastructure (OMI) CIM サーバーのバージョン 1.0.8.1 が必要です。 OMI は、Open Group: [Open Management Infrastructure (OMI)](https://collaboration.opengroup.org/omi/) からダウンロードできます。

OMI をインストールするには、Linux システムに適したパッケージ (.rpm または .deb)、OpenSSL バージョンに適したパッケージ (ssl_098 または ssl_100)、およびアーキテクチャに適したパッケージ (x86/x64) をインストールします。 CentOS、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、および Oracle Linux には、RPM パッケージが適しています。 Debian GNU/Linux および Ubuntu Server には、DEB パッケージが適しています。 OpenSSL 0.9.8 がインストールされているコンピューターには ssl_098 パッケージが適し、OpenSSL 1.0 がインストールされているコンピューターには ssl_100 パッケージが適しています。

> **注**: インストールされている OpenSSL のバージョンを調べるには、コマンド `openssl version` を実行します。

CentOS 7 x64 システムに OMI をインストールするには、次のコマンドを実行します。

`# sudo rpm -Uvh omiserver-1.0.8.ssl_100.rpm`

### DSC のインストール

Linux 用の DSC は、[こちら](https://github.com/Microsoft/PowerShell-DSC-for-Linux/releases/latest)からダウンロードできます。 

DSC をインストールするには、Linux システムに適したパッケージ (.rpm または .deb)、OpenSSL バージョンに適したパッケージ (ssl_098 または ssl_100)、およびアーキテクチャに適したパッケージ (x86/x64) をインストールします。 CentOS、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、および Oracle Linux には、RPM パッケージが適しています。 Debian GNU/Linux および Ubuntu Server には、DEB パッケージが適しています。 OpenSSL 0.9.8 がインストールされているコンピューターには ssl_098 パッケージが適し、OpenSSL 1.0 がインストールされているコンピューターには ssl_100 パッケージが適しています。

> **注**: インストールされている OpenSSL のバージョンを調べるには、コマンド openssl version を実行します。
 
CentOS 7 x64 システムに DSC をインストールするには、次のコマンドを実行します。

`# sudo rpm -Uvh dsc-1.0.0-254.ssl_100.x64.rpm`


## Linux 用 DSC の使用

次のセクションでは、Linux コンピューターで DSC 構成を作成し、実行する方法を説明します。

### 構成 MOF ドキュメントの作成

Linux コンピューターの構成を作成するには、Windows コンピューターの場合と同じように、Windows PowerShell 構成キーワードを使用します。 次の手順では、Windows PowerShell を使用して Linux コンピューターの構成ドキュメントを作成する方法について説明します。

1. nx モジュールのインポート nx Windows PowerShell モジュールには Linux 用 DSC の組み込みリソースのスキーマが含まれており、このモジュールをローカル コンピューターにインストールし、構成にインポートする必要があります。

    - nx モジュールをインストールするには、nx モジュール ディレクトリを `$env:USERPROFILE\Documents\WindowsPowerShell\Modules\` または `$PSHOME\Modules` にコピーします。 nx モジュールは、Linux 用 DSC のインストール パッケージ (MSI) に含まれています。 構成に nx モジュールをインポートするには、__Import-DSCResource__ コマンドを使用します。
    
```powershell
Configuration ExampleConfiguration{
   
    Import-DSCResource -Module nx

}
```
2. 構成を定義し、構成ドキュメントを生成します。

```powershell
Configuration ExampleConfiguration{
   
    Import-DscResource -Module nx
 
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

### Linux コンピューターへの構成のプッシュ

構成ドキュメント (MOF ファイル) を Linux コンピューターにプッシュするには、[Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットを使用します。 Linux コンピューターに対してリモートからこのコマンドレットを [Get-DscConfiguration](https://technet.microsoft.com/en-us/library/dn407379).aspx と共に使用するか、または [Test-DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) コマンドレットを使用するためには、CIMSession を使用する必要あります。 Linux コンピューターに CIMSession を作成するには、[New-CimSession](https://technet.microsoft.com/en-us/library/jj590760.aspx) コマンドレットを使用します。

次のコードは、Linux 用 DSC の CIMSession を作成する方法を示しています。

```powershell
$Node = "ostc-dsc-01"
$Credential = Get-Credential -UserName:"root" -Message:"Enter Password:"

#Ignore SSL certificate validation
#$opt = New-CimSessionOption -UseSsl:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

#Options for a trusted SSL certificate
$opt = New-CimSessionOption -UseSsl:$true 
$Sess=New-CimSession -Credential:$credential -ComputerName:$Node -Port:5986 -Authentication:basic -SessionOption:$opt -OperationTimeoutSec:90 
```

> **注**:
* "プッシュ" モードの場合、ユーザーの資格情報は Linux コンピューターの root ユーザーである必要があります。
* Linux 用 DSC では SSL/TLS 接続のみがサポートされているため、New-CimSession を使用するときは、-UseSSL パラメーターを $true に設定する必要があります。
* OMI (DSC 用) で使用される SSL 証明書は、pemfile プロパティと keyfile プロパティを使用して `/opt/omi/etc/omiserver.conf` ファイルに指定します。
[New-CimSession](https://technet.microsoft.com/en-us/library/jj590760.aspx) コマンドレットを実行している Windows コンピューターでこの証明書が信頼されていない場合は、`-SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true` の各 CIMSession オプションを使用して証明書の検証を無視することを選択できます。

Linux ノードに DSC 構成をプッシュするには、次のコマンドを実行します。

`Start-DscConfiguration -Path:"C:\temp" -CimSession:$Sess -Wait -Verbose`

### プル サーバーを使用した構成の配布

構成を Linux コンピューターに配布するには、Windows コンピューターの場合と同じく、プル サーバーを使用できます。 プル サーバーを使用する方法の詳細については、「[DSC Web プル サーバーのセットアップ](pullServer.md)」を参照してください。 プル サーバーでの Linux コンピューターの使用に関する追加情報および制限事項については、Linux 用 Desired State Configuration のリリース ノートをご覧ください。

### ローカルでの構成の操作

Linux 用 DSC には、ローカルの Linux コンピューターから構成を操作するためのスクリプトが含まれています。 これらのスクリプトは、`/opt/microsoft/dsc/Scripts` にあり、次のものが含まれています。
* GetDscConfiguration.py

 コンピューターに適用される現在の構成を返します。 Windows PowerShell コマンドレットである Get-DscConfiguration に似ています。

`# sudo ./GetDscConfiguration.py`

* GetDscLocalConfigurationManager.py

 コンピューターに適用される現在のメタ構成を返します。 [Get-DSCLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx) コマンドレットに似ています。

`# sudo ./GetDscLocalConfigurationManager.py`

* InstallModule.py

 カスタムの DSC リソース モジュールをインストールします。 モジュール共有オブジェクト ライブラリおよびスキーマ MOF ファイルが含まれている .zip ファイルへのパスが必要です。

`# sudo ./InstallModule.py /tmp/cnx_Resource.zip`

* RemoveModule.py

 カスタムの DSC リソース モジュールを削除します。 削除するモジュールの名前が必要です。

`# sudo ./RemoveModule.py cnx_Resource`

* StartDscLocalConfigurationManager.py 

 構成 MOF ファイルをコンピューターに適用します。 [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットに似ています。 適用する構成 MOF へのパスが必要です。

`# sudo ./StartDscLocalConfigurationManager.py –configurationmof /tmp/localhost.mof`

* SetDscLocalConfigurationManager.py

 メタ構成 MOF ファイルをコンピューターに適用します。 [Set-DSCLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn521621.aspx) コマンドレットに似ています。 適用するメタ構成 MOF へのパスが必要です。

`# sudo ./SetDscLocalConfigurationManager.py –configurationmof /tmp/localhost.meta.mof`

## Linux 用 PowerShell Desired State Configuration のログ ファイル

Linux 用 DSC のメッセージのために次のログ ファイルが生成されます。

|ログ ファイル|Directory|説明|
|---|---|---|
|omiserver.log|/var/opt/omi/log|OMI CIM サーバーの操作に関連するメッセージ。|
|dsc.log|/var/opt/omi/log|ローカル構成マネージャー (LCM) と DSC リソースの操作に関連するメッセージ。|




<!--HONumber=Oct16_HO1-->


