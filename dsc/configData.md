---
title: "構成データと環境データの分離"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 8a3ae5fdf5d70de999ca6b992efb14533408c305

---

# 構成データと環境データの分離

>適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

Windows PowerShell Desired State Configuration (DSC) では、構成のロジックから構成データを分離することができます。 見方を変えると、このことは、構造の構成 (たとえば、IIS がインストールされていることが必要な構成) と環境の構成を切り離して考えることに相当します。つまり、テスト環境の場合と、運用環境の場合とで、ノード名が異なっても、適用される構成は同じになります。 このことには次の利点があります。

* さまざまなリソース、ノード、および構成の構成データを再利用できます。
* 構成ロジックは、ハード コードされたデータが含まれていないと、再利用可能性が高くなります。 これは、関数の適切なスクリプト作成のガイドラインに似ています。
* 一部のデータを変更する必要がある場合は、1 つの場所で変更を加えることができるため、時間を節約し、エラーを削減できます。

## 基本的な概念と例

構成の環境部分を指定するには、DSC で **ConfigurationData** パラメーターを使用します。これは、ハッシュ テーブルです (またはハッシュ テーブルを含む .psd1 ファイルを指定できます)。 このハッシュ テーブルには、少なくとも、構造化された値を持つ 1 つのキー **AllNodes** が必要です。 たとえば、次のように入力します。

```powershell
$MyData = 
@{
    AllNodes = @()
    NonNodeData = ""   
}
```

値が配列である AllNodes キーに注意してください。 この配列の各要素も、キーとして NodeName を必要とするハッシュ テーブルです。

```powershell
$MyData = 
@{
    AllNodes = 
    @(
        @{
            NodeName = "VM-1"
        },

 
        @{
            NodeName = "VM-2"
        },

 
        @{
            NodeName = "VM-3"
        }
    );

    NonNodeData = ""   
}
```

AllNodes の各ハッシュ テーブル エントリは、構成内のノードの構成データに対応します。 必要な NodeName キーに加えて、他のキーをハッシュ テーブルに追加することもできます。次に例を示します。

```powershell
$MyData = 
@{
    AllNodes = 
    @(
        @{
            NodeName = "VM-1"
            Role     = "WebServer"
        },

 
        @{
            NodeName = "VM-2"
            Role     = "SQLServer"
        },

 
        @{
            NodeName = "VM-3"
            Role     = "WebServer"
        }
    );

    NonNodeData = ""   
}
```

DSC には、構成スクリプトで使用する 3 つの特殊な変数が用意されています。

* **$AllNodes**: すべてのノードを参照します。 **.Where()** および **.ForEach()** によるフィルター処理を使用できます。このため、ノード名ハード コードしてアクション用の特定のノードを選択する代わりに、次のように記述して上記の例の VM 1 および VM 3 を選択できます。

```powershell
configuration MyConfiguration
{
    node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
    }
}
```

* **$Node**: ノードのセットがフィルター処理された後、$Node を使用して特定のエントリを参照できます。 たとえば、次のように入力します。

```powershell
configuration MyConfiguration
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite
    node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = "Present"
        }
    }
}
```

すべてのノードにプロパティを適用するには、NodeName = "*" を設定します。 たとえば、すべてのノードに LogPath プロパティを適用するには、次のように入力します。

```
$MyData = 
@{
    AllNodes = 
    @(
        @{
            NodeName           = "*"
            LogPath            = "C:\Logs"
        },

 
        @{
            NodeName = "VM-1"
            Role     = "WebServer"
            SiteContents = "C:\Site1"
            SiteName = "Website1"
        },

 
        @{
            NodeName = "VM-2"
            Role     = "SQLServer"
        },

 
        @{
            NodeName = "VM-3"
            Role     = "WebServer"
            SiteContents = "C:\Site2"
            SiteName = "Website3"
        }
    );
}
```

* **$ConfigurationData**: 構成内でこの変数を使用して、パラメーターとして渡された構成データのハッシュ テーブルにアクセスすることができます。 たとえば、次のように入力します。

```powershell
$MyData = 
@{
    AllNodes = 
    @(
        @{
            NodeName           = "*"
            LogPath            = "C:\Logs"
        },

 
        @{
            NodeName = "VM-1"
            Role     = "WebServer"
            SiteContents = "C:\Site1"
            SiteName = "Website1"
        },

 
        @{
            NodeName = "VM-2"
            Role     = "SQLServer"
        },
 

        @{
            NodeName = "VM-3"
            Role     = "WebServer"
            SiteContents = "C:\Site2"
            SiteName = "Website3"
        }
    );

    NonNodeData = 
    @{
        ConfigFileContents = (Get-Content C:\Template\Config.xml)
     }   
} 

configuration MyConfiguration
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = "Present"
        }

        File ConfigFile
        {
            DestinationPath = $Node.SiteContents + "\\config.xml"
            Contents = $ConfigurationData.NonNodeData.ConfigFileContents
        }
    }
}
```

含まれる完全な例は、[xWebAdministration module (xWebAdministration モジュール)](https://powershellgallery.com/packages/xWebAdministration) にあります。




<!--HONumber=Oct16_HO1-->


