---
title: "DSC Web プル サーバーのセットアップ"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 7bbfc31fdebdde83ac1784373b51af40b1dc9492

---

# DSC Web プル サーバーのセットアップ

> 適用先: Windows PowerShell 5.0

DSC Web プル サーバーは、OData インターフェイスを使用してターゲット ノードからの要求に応じて DSC 構成ファイルをそのターゲット ノードで使用できるようにする、IIS 内の Web サービスです。

プル サーバーを使用するための要件:

* 次のものを実行するサーバー。
  - WMF/PowerShell 5.0 以降
  - IIS サーバー ロール
  - DSC サービス
* 理想としては、証明書を生成する何らかの手段。これは、ターゲット ノードのローカル構成マネージャー (LCM) に渡された資格情報をセキュリティ保護するためのものです。

IIS サーバー ロールと DSC サービスを追加するには、サーバー マネージャーで役割と機能の追加ウィザードを使用するか、または PowerShell を使用します。 どちらの手順も、このトピックに含まれているサンプル スクリプトが自動的に処理します。

## XWebService リソースの使用
Web プル サーバーをセットアップする最も簡単な方法は、xPSDesiredStateConfiguration モジュールに含まれる xWebService リソースを使用することです。 次の手順では、Web サービスをセットアップする構成でリソースを使用する方法について説明します。

1. [Install-Module](https://technet.microsoft.com/en-us/library/dn807162.aspx) コマンドレットを呼び出して、**xPSDesiredStateConfiguration** モジュールをインストールします。 **注**: **Install-Module** は、PowerShell 5.0 に含まれている **PowerShellGet** モジュールに含まれています。 「[PackageManagement PowerShell Modules Preview (PackageManagement PowerShell モジュールのプレビュー)](https://www.microsoft.com/en-us/download/details.aspx?id=49186)」で PowerShell 3.0 と 4.0 の **PowerShellGet** モジュールをダウンロードできます。 
1. DSC プル サーバーの SSL 証明書を、自社組織内またはパブリック証明機関のいずれかの信頼された証明機関から取得します。 証明機関から受け取る証明書は、通常、PFX 形式です。 証明書は、DSC プル サーバーになるノードの既定の場所 (CERT:\LocalMachine\My である必要があります) にインストールします。 証明書の拇印をメモしておきます。
1. 登録キーとして使う GUID を選択します。 PowerShell を使って GUID を生成するには、PS プロンプトに「``` [guid]::newGuid()```」と入力し、Enter キーを押します。 このキーは、登録時にクライアント ノードによって認証のために共有キーとして使用されます。 詳細については、この後の「[登録キー](#RegKey)」セクションをご覧ください。
1. PowerShell ISE で、次の構成スクリプトを起動 (F5) します (このスクリプトは、**xPSDesiredStateConfiguration** モジュールの Example フォルダーに Sample_xDscWebService.ps1 として存在します)。 このスクリプトは、プル サーバーをセットアップします。
  
```powershell
configuration Sample_xDscPullServer
{ 
    param  
    ( 
            [string[]]$NodeName = 'localhost', 
            
            [ValidateNotNullOrEmpty()] 
            [string] $certificateThumbPrint,
            
            [Parameter(Mandatory)]
            [ValidateNotNullOrEmpty()]
            [string] $RegistrationKey 
     ) 
 
 
     Import-DSCResource -ModuleName xPSDesiredStateConfiguration 

     Node $NodeName 
     { 
         WindowsFeature DSCServiceFeature 
         { 
             Ensure = 'Present'
             Name   = 'DSC-Service'             
         } 
 
         xDscWebService PSDSCPullServer 
         { 
             Ensure                  = 'Present' 
             EndpointName            = 'PSDSCPullServer' 
             Port                    = 8080 
             PhysicalPath            = "$env:SystemDrive\inetpub\PSDSCPullServer" 
             CertificateThumbPrint   = $certificateThumbPrint          
             ModulePath              = "$env:PROGRAMFILES\WindowsPowerShell\DscService\Modules" 
             ConfigurationPath       = "$env:PROGRAMFILES\WindowsPowerShell\DscService\Configuration" 
             State                   = 'Started'
             DependsOn               = '[WindowsFeature]DSCServiceFeature'                         
         } 

        File RegistrationKeyFile
        {
            Ensure          = 'Present'
            Type            = 'File'
            DestinationPath = "$env:ProgramFiles\WindowsPowerShell\DscService\RegistrationKeys.txt"
            Contents        = $RegistrationKey
        }
    }
}

```

1. 構成を実行します。このとき、SSL 証明書の拇印を **certificateThumbPrint** パラメーターとして渡し、GUID 登録キーを **RegistrationKey** パラメーターとして渡します。

```powershell
# To find the Thumbprint for an installed SSL certificate for use with the pull server list all certifcates in your local store 
# and then copy the thumbprint for the appropriate certificate by reviewing the certificate subjects
dir Cert:\LocalMachine\my

# Then include this thumbprint when running the configuration
Sample_xDSCPullServer -certificateThumbprint 'A7000024B753FA6FFF88E966FD6E19301FAE9CCC' -RegistrationKey '140a952b-b9d6-406b-b416-e0f759c9c0e4' -OutputPath c:\Configs\PullServer

# Run the compiled configuration to make the target node a DSC Pull Server
Start-DscConfiguration -Path c:\Configs\PullServer -Wait -Verbose
```

## 登録キー
サーバーにクライアント ノードを登録して、構成 ID の代わりに構成名を使用できるように、上の構成で作成された登録キーは、`C:\Program Files\WindowsPowerShell\DscService` にある `RegistrationKeys.txt` という名前のファイルに保存されます。 登録キーは、クライアントがプル サーバーに初期登録を行うときに共有シークレットとして機能します。 登録が正常に完了すると、クライアントは、プル サーバーに一意に認証されるために使う自己署名証明書を生成します。 この証明書の拇印がローカルに保存され、プル サーバーの URL に関連付けられます。
> **注**: 登録キーは、PowerShell 4.0 ではサポートされていません。 

プル サーバーで認証するようにノードを構成するには、登録キーが、このプル サーバーに登録されるすべてのターゲット ノードのメタ構成に含まれている必要があります。 なお、次のメタ構成にある **RegistrationKey** は、ターゲット コンピューターが正常に登録された後に削除され、値 '140a952b-b9d6-406b-b416-e0f759c9c0e4' はプル サーバーの RegistrationKeys.txt ファイルに格納されている値と一致する必要があります。 登録キー値を知ると、任意のターゲット コンピューターをプル サーバーに登録できるようになるので、登録キー値は常にセキュリティで保護してください。

```powershell
[DSCLocalConfigurationManager()]
configuration PullClientConfigID
{
    Node localhost
    {
        Settings
        {
            RefreshMode          = 'Pull'
            RefreshFrequencyMins = 30 
            RebootNodeIfNeeded   = $true
        }
        
        ConfigurationRepositoryWeb CONTOSO-PullSrv
        {
            ServerURL          = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey    = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
            ConfigurationNames = @('ClientConfig')
        }   
        
        ReportServerWeb CONTOSO-PullSrv
        {
            ServerURL       = 'https://CONTOSO-PullSrv:8080/PSDSCPullServer.svc'
            RegistrationKey = '140a952b-b9d6-406b-b416-e0f759c9c0e4'
        }
    }
}

PullClientConfigID -OutputPath c:\Configs\TargetNodes
```
> **注**: **ReportServerWeb** セクションでは、レポートするデータをプル サーバーに送信できるようにしています。 

メタ構成ファイルに **ConfigurationID** プロパティがないことは、暗黙的に、そのプル サーバーが V2 バージョンのプル サーバー プロトコルをサポートしていることを示すので、初期登録が必要になります。 逆に、**ConfigurationID** が存在することは、V1 バージョンのプル サーバー プロトコルが使用され、登録処理がないことを意味します。

>**注**: プッシュのシナリオでは、現在のリリースにバグが存在するため、プル サーバーに登録されていないノードについても、メタ構成ファイルで ConfigurationID プロパティを定義する必要があります。 こうすると、V1 プル サーバー プロトコルが強制されるので、登録エラーのメッセージが表示されません。

## 構成とリソースの配置
プル サーバーのセットアップが完了すると、プル サーバーの構成で **ConfigurationPath** プロパティと **ModulePath** プロパティによって定義されているフォルダーは、プルするためにターゲット ノードで使用可能にするモジュールと構成を配置する場所になります。 これらのファイルをプル サーバーが正しく処理するためには、特定の形式である必要があります。 

### DSC リソース モジュールのパッケージの形式
各リソース モジュールは、圧縮し、**{モジュール名}_{モジュールのバージョン}.zip** というパターンで名前を付ける必要があります。 たとえば、モジュール名が xWebAdminstration で、バージョンが 3.1.2.0 のモジュールでは、'xWebAdministration_3.2.1.0.zip' という名前になります。 各バージョンのモジュールを 1 つの zip ファイルに含める必要があります。 各 zip ファイルには 1 つのバージョンのリソースのみが含まれるので、WMF 5.0 で追加された、単一のディレクトリに複数のモジュール バージョンを入れるモジュール形式はサポートされていません。 このため、プル サーバーで使うための DSC リソース モジュールをパッケージ化する前に、ディレクトリ構造に少しの変更が必要です。 WMF 5.0 の DSC リソースを含むモジュールの既定の形式は、'{モジュール フォルダー}\{モジュールのバージョン}\DscResources\{DSC リソース フォルダー}\' です。 プル サーバー用にパッケージ化する前には、単純に **{モジュールのバージョン}** フォルダーを削除して、'{モジュール フォルダー}\DscResources\{DSC リソース フォルダー}\' というパスにします。 この変更を加えた後、上で説明したようにフォルダーを zip 圧縮し、これらの zip ファイルを **ModulePath** フォルダーに置きます。

### 構成 MOF の形式 
ターゲット ノード上の LCM が構成を検証できるように、構成 MOF ファイルはチェックサム ファイルと組み合わせて使用する必要があります。 チェックサムを作成するには、[New-DSCCheckSum](https://technet.microsoft.com/en-us/library/dn521622.aspx) コマンドレットを呼び出します。 このコマンドレットは、構成 MOF が存在するフォルダーが指定された **Path** パラメーターを受け取ります。 このコマンドレットは、`ConfigurationMOFName.mof.checksum` という名前でチェックサム ファイルを作成します。ここで、`ConfigurationMOFName` は構成 MOF ファイルの名前です。 指定のフォルダーに複数の構成 MOF ファイルがある場合は、そのフォルダー内の構成ごとにチェックサムが作成されます。 MOF ファイルと、それに関連するチェックサム ファイルは、**ConfigurationPath** フォルダーに配置します。

>**注**: 何らかの方法で構成 MOF ファイルを変更した場合は、チェックサム ファイルも作成し直す必要があります。

## ツール
プル サーバーのセットアップ、検証、管理を簡素化するために、次のツールが、xPSDesiredStateConfiguration モジュールの最新バージョンに例として含まれています。
1. プル サーバーに使用する DSC リソース モジュールおよび構成ファイルをパッケージ化するために役立つモジュール。 [PublishModulesAndMofsToPullServer.psm1](https://github.com/PowerShell/xPSDesiredStateConfiguration/blob/dev/DSCPullServerSetup/PublishModulesAndMofsToPullServer.psm1)。 次の例です。

```powershell
    # Example 1 - Package all versions of given modules installed locally and MOF files are in c:\LocalDepot
     $moduleList = @("xWebAdministration", "xPhp") 
     Publish-DSCModuleAndMof -Source C:\LocalDepot -ModuleNameList $moduleList 
     
     # Example 2 - Package modules and mof documents from c:\LocalDepot
     Publish-DSCModuleAndMof -Source C:\LocalDepot -Force
```

1. プル サーバーが正しく構成されていることを検証するスクリプト。 [PullServerSetupTests.ps1](https://github.com/PowerShell/xPSDesiredStateConfiguration/blob/dev/Examples/PullServerDeploymentVerificationTest/PullServerSetupTests.ps1)。


## プル クライアントの構成 
次のトピックでは、プル クライアントのセットアップについて詳しく説明します。

* [構成 ID を使用したプル クライアントのセットアップ](pullClientConfigID.md)
* [構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md)
* [部分構成](partialConfigs.md)


## 関連項目
* [Windows PowerShell Desired State Configuration の概要](overview.md)
* [構成の適用](enactingConfigurations.md)
* [DSC のレポート サーバーを使用します。](reportServer.md)




<!--HONumber=Oct16_HO1-->


