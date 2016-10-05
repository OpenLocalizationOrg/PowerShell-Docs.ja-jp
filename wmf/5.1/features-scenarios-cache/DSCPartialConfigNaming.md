---
title: "部分構成命名規則のプル"
author: jaimeo
contributor: berheabrha
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 368c26766961e760fd2de8c99057121bea076158

---

##部分構成命名規則のプル
以前のリリースでは、部分的な構成の名前付け規則は、プル サーバー/service で mof ファイル名は、さらに、MOF ファイルに埋め込まれている構成の名前と一致する必要がローカル構成マネージャーの設定で指定された部分の構成名を一致する必要がありますがでした。 下のスナップショットを参照してください:- •   ローカル構成設定により、あるノードに受信を許可する部分構成が定義されます。

![サンプルのメタ構成](../../images/MetaConfigPartialOne.png)

•   サンプルの部分構成定義 

```Powershell
Configuration PartialOne
{
    Node('localhost')
    {
        File test 
        {
            DestinationPath = "$env:TEMP\partialconfigexample.txt"
            Contents = 'Partial Config Example'
        }
    }
}
PartialOne
```

•   生成された MOF ファイルに組み込まれている ‘ConfigurationName’。

![生成された mof ファイルのサンプル](../../images/PartialGeneratedMof.png)

•   プル構成リポジトリの FileName 

![構成リポジトリの FileName](../../images/PartialInConfigRepository.png)

Azure Automation サービス名の生成の mof ファイルとして ``<ConfigurationName>.<NodeName>.mof``します。 そのため、下の構成は PartialOne.Localhost.mof にコンパイルされます。  
これによりされて Azure automation サービスからの部分的な構成をプルすることは不可能です。

```Powershell
Configuration PartialOne
{
    Node('localhost')
    {
        File test 
        {
            DestinationPath = "$env:TEMP\partialconfigexample.txt"
            Contents = 'Partial Config Example'
        }
    }
}
PartialOne
```

WMF 5.1 では、プル サーバー/サービスの部分構成の名前を ``<ConfigurationName>.<NodeName>.mof`` にできます。 さらに、プルから 1 つの構成をプルしているマシン プル サーバー構成のリポジトリの構成ドキュメントでは、サーバーには任意の名前ができます。 この名前付けのような柔軟性によりと内部設置型と Azure Automation プル サービスの両方を使用してノードの部分的な構成を管理できます。 たとえば、ローカルにプッシュされます 'base' の部分的な構成と他の部分的な構成を Azure Automation サービスからプルことができます。

以下のメタ構成では、ローカルと Azure Automation サービスの両方で管理されるようにノードが設定されます。

```Powershell
  [DscLocalConfigurationManager()]
   Configuration RegistrationMetaConfig
   {
        Settings
        {
            RefreshFrequencyMins = 30;
            RefreshMode = "PULL";            
        }

        ConfigurationRepositoryWeb web
        {
            ServerURL =  $endPoint
            RegistrationKey = $registrationKey
            ConfigurationNames = $configurationName
        }

        # Partial configuration managed by Azure Automation Service.
        PartialConfiguration PartialCOnfigurationManagedByAzureAutomation
        {
            ConfigurationSource = "[ConfigurationRepositoryWeb]Web"   
        }
    
        # This partial configuration is managed locally.
        PartialConfiguration OnPremConfig
        {
            RefreshMode = "PUSH"
            ExclusiveResources = @("Script")
        }

   }

   RegistrationMetaConfig
   slcm -Path .\RegistrationMetaConfig -Verbose
 ```





<!--HONumber=Oct16_HO1-->


