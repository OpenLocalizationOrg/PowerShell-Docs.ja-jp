#構成および環境データを分離すること

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

Windows PowerShell 必要な状態 Configuration (DSC)、構成データを構成のロジックから分離することができます。 構造型の構成を検討するのには、これを確認する方法 (たとえば、構成必要があります IIS をインストールする) 環境の構成とは別に (つまり状況がいずれかの運用環境とテスト環境はかどうか、異なりますが、ノード名になりますが、それらに適用される構成は同じになります)。 これは、次の利点があります。

* さまざまなリソース、ノード、および構成の構成データを再利用することができます。
* 構成のロジックは、ハード コーディングされたデータが含まれていない場合は、再利用可能です。 これは、機能は、関数の適切なスクリプト作成のガイドラインに似ています。
* データの一部を変更する必要がある場合、することができます、変更、1 つの場所に時間を節約し、エラーを減らすためです。

##基本的な概念と例

DSC は、構成の環境の一部を指定する、 **ConfigurationData** パラメーター、これは、ハッシュ テーブル (または、ハッシュ テーブルを含む .psd1 ファイルがかかることができます)。 このハッシュ テーブルに少なくとも 1 つのキーが必要 **AllNodes**, を持つ、構造化された値。 たとえば、次のように入力します。

```powershell
$MyData = 
@{
    AllNodes = @();
    NonNodeData = ""   
}
```

AllNodes キーは、ある値が配列に注意してください。 この配列の各要素は、キーとしてのノード名を必要とするハッシュ テーブルもです。

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

AllNodes で各ハッシュ テーブルのエントリは、構成データを構成内のノードに対応します。 だけでなく、必要なノード名のキーを追加することも他のキー、ハッシュ テーブルの。

```powershell
$MyData = 
@{
    AllNodes = 
    @(
        @{
            NodeName = "VM-1";

        },


        @{
            NodeName = "VM-2";
            Role     = "SQLServer"
        },


        @{
            NodeName = "VM-3";
            Role     = "WebServer"
        }
    );

    NonNodeData = ""   
}
```

DSC には、構成のスクリプトで使用する 3 つの特殊な変数が用意されています。

* **$AllNodes**: すべてのノードを参照します。 によるフィルター処理を行うこともできます **です。Where()** と **です。ForEach()**, ので、アクションの特定のノードを選択するハード コーディング ノード名の代わりに、上記の例では、VM および VM 3 を選択する次のように何かを記述できます。

```powershell
configuration MyConfiguration
{
    node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
    }
}
```

* **$Node**: 後、ノードのセットをフィルター選択すると、特定のエントリを参照する $Node を行うこともできます。 たとえば、次のように入力します。

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

すべてのノードには、プロパティを適用するには、ノード名を設定することができます ="*"です。 たとえばにすべてのノードを LogPath プロパティに与えるには、こうとでした。

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
            NodeName = "VM-1";
            Role     = "WebServer"
            SiteContents = "C:\Site1"
            SiteName = "Website1"
        },


        @{
            NodeName = "VM-2";
            Role     = "SQLServer"
        },


        @{
            NodeName = "VM-3";
            Role     = "WebServer";
            SiteContents = "C:\Site2"
            SiteName = "Website3"
        }
    );
}
```

* **$ConfigurationData**: パラメーターとして渡された構成データのハッシュ テーブルにアクセスする、構成内には、この変数を使用することができます。 たとえば、次のように入力します。

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
            NodeName = "VM-1";
            Role     = "WebServer"
            SiteContents = "C:\Site1"
            SiteName = "Website1"
        },


        @{
            NodeName = "VM-2";
            Role     = "SQLServer"
        },


        @{
            NodeName = "VM-3";
            Role     = "WebServer";
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

含まれる完全な例を見つけることができます、 [xWebAdministration モジュール](https://powershellgallery.com/packages/xWebAdministration)です。



