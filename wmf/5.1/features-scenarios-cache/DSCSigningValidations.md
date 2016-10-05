---
title: "DSC のモジュールと構成の署名検証"
author: jaimeo
contributor: berheabra
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 817fadb79716e41ce8cc8f4245dedc66347ac413

---

##DSC のモジュールと構成の署名検証
DSC の構成に関するドキュメントとモジュールが、プル サーバーまたは (Azure Automation サービスを使用) の場合はプル サーバーから管理対象のコンピュータに配布されます。 プル サーバー/サービスが侵害された場合、攻撃者できる可能性のあるプル サーバー上のモジュールと構成を変更をしてすべての管理対象コンピューターに配布ため、顧客のさらに多くのマシンを損なうこと。 

 このような脅威が WMF 5.1 でアドレス指定します。 DSC は、モジュールと構成にデジタル署名の検証をサポートしています (します。MOF) ファイル。 この機能は、ノードを構成または信頼された署名者を署名がないかが改ざんされている信頼された署名者によって署名された後、モジュール ファイルを実行できなくなります。 



###構成とモジュールに署名する方法 
***
1. 構成ファイル (.MOFS):- 既存の PowerShell コマンドレット [Set-AuthenticodeSignature](https://technet.microsoft.com/library/hh849819.aspx) が拡張され、MOF ファイルの署名に対応しています。  
2. モジュール:- モジュールの署名は、次の手順を利用し、対応するモジュール カタログに署名することで完了します:- 
    * カタログ ファイルの作成: カタログ ファイルには、暗号法のハッシュまたは拇印の集まりが含まれています。 拇印はそれぞれ、モジュールに含まれるファイルに対応します。  新しいコマンドレット [New-FileCatalog](https://technet.microsoft.com/library/cc732148.aspx) が追加され、ユーザーは自分のモジュールのカタログ ファイルを作成できます。 カタログのコマンドレットを参照してください [相対リンク](catalog-cmdlets.md) カタログ ファイルを作成します。 
    * カタログ ファイルの署名: [Set-AuthenticodeSignature](https://technet.microsoft.com/library/hh849819.aspx) を利用し、カタログ ファイルに署名します。
    * モジュール フォルダー内にカタログ ファイルを配置します。
慣例では、モジュール カタログ ファイルは、モジュールと同じ名前のモジュール フォルダーの下に配置する必要があります。

###LocalConfigurationManager 設定で署名検証を有効にする

####プル
ノードの LocalConfigurationManager は、その現在の設定に基づき、モジュールと構成の署名を検証します。 既定では、署名検証は無効です。 署名検証は、‘SignatureValidation’ ブロックを下の図のようにノードのメタ構成定義に追加することで有効にできます:-

```PowerShell
[DSCLocalConfigurationManager()]
Configuration EnableSignatureValidation
{
    Settings
    {
        RefreshMode = 'PULL'        
    } 
    
    ConfigurationRepositoryWeb pullserver{
      ConfigurationNames = 'sql'
      ServerURL = 'http://localhost:8080/PSDSCPullServer/PSDSCPullServer.svc'
      AllowUnsecureConnection = $true
      RegistrationKey = 'd6750ff1-d8dd-49f7-8caf-7471ea9793fc' # Replace this with correct registration key.
    }
    SignatureValidation validations{
        # By default,LCM will use default Windows trusted publisher store to validate the certificate chain. If TrustedStorePath property is specified, LCM will use this custom store for retrieving the trusted publishers to validate the content.
        TrustedStorePath = 'Cert:\LocalMachine\DSCStore'            
        SignedItemType =  'Configuration','Module'         # Those are list of DSC artifacts, for which LCM need to verify their digital signature before executing them on the node.       
    }
 
}
EnableSignatureValidation
Set-DscLocalConfigurationManager -Path .\EnableSignatureValidation -Verbose 
 ```

ノードに上記のメタ構成を設定すると、ダウンロードした構成とモジュールで署名を検証できます。 ローカル構成マネージャーは次の手順でデジタル署名を検証します。
* 構成ファイル (.MOF) の署名が有効であることを検証します。 これは PowerShell コマンドレット [Get-AuthenticodeSignature](https://technet.microsoft.com/library/hh849805.aspx) を使用します。このコマンドレットは 5.1 で拡張され、MOF 署名検証に対応しています。
* 署名者が信頼できることを認定した証明機関を検証します。
* 構成のモジュール/リソース依存性を一時的な場所にダウンロードします。
* モジュール内に含まれるカタログの署名を検証します。
    * <moduleName>.cat ファイルを見つけ、コマンドレット [Get-AuthenticodeSignature](https://technet.microsoft.com/library/hh849805.aspx) でその署名を検証します。
    * 署名者が信頼できることを認定した証明機関を検証します。
    * 新しいコマンドレット [Test-FileCatalog](https://technet.microsoft.com/library/cc732148.aspx) を利用し、モジュールのコンテンツが改ざんされていないことを検証します。
* $env:ProgramFiles\WindowsPowerShell\Modules\ に Install-module を実行します
* プロセス構成

注: - モジュール カタログと構成の署名検証は、構成がシステムに最初に適用されるときか、モジュールがダウンロードされ、インストールされるときにのみ実行されます。 整合性実行では、Current.mof やそのモジュール依存性の署名は検証されません。
インスタンスの構成、pullserver から取り出されている場合が署名されていない、構成の処理がすべて一時的な下に表示されるエラーで終了しのいずれかの段階で検証に失敗した場合は、ファイルが削除されます。

![構成のエラー出力のサンプル](../../images/PullUnsignedConfigFail.PNG)

同様に、カタログに署名のないモジュールがプルされると次のエラーが発生します:-

![モジュールのエラー出力のサンプル](../../images/PullUnisgnedCatalog.PNG)

####プッシュ
ノードに配信される前に、プッシュを使用して提供される構成をそのソースで改ざんされる可能性があります。 ローカル構成マネージャーは、プッシュまたは公開された構成に対して同様の署名検証手順を実行します。
以下は、プッシュの署名検証の完全例です。

* ノードで署名検証を有効にします。

```Powershell
[DSCLocalConfigurationManager()]
Configuration EnableSignatureValidation
{
    Settings
    {
        RefreshMode = 'PUSH'        
    } 
    SignatureValidation validations{
        TrustedStorePath = 'Cert:\LocalMachine\DSCStore'   
        SignedItemType =  'Configuration','Module'             
    }

}
EnableSignatureValidation
Set-DscLocalConfigurationManager -Path .\EnableSignatureValidation -Verbose
``` 
* サンプル構成ファイルを作成します。

```Powershell
# Sample configuration
Configuration Test{

    File foo
    {
        DestinationPath =  "$env:TEMP\signingTest.txt"
        Contents = "ABC"
    }
}
Test
```

* 署名のない構成ファイルをノードにプッシュしてみます。 

```Powershell
Start-DscConfiguration -Path .\Test -Wait -Verbose -Force
``` 
![ErrorUnsignedMofPushed](../../images/PushUnsignedMof.PNG)

* コード署名証明書を利用し、構成ファイルに署名します。

![SignMofFile](../../images/SignMofFile.PNG)

* 署名された mof ファイルをプッシュしてみます。

![SignMofFile](../../images/PushSignedMof.PNG)




<!--HONumber=Oct16_HO1-->


