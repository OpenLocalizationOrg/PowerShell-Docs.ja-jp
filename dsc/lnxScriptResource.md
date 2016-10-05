---
title: "Linux 用 DSC の nxScript リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4c575bbf0e0553e19e56bcc6edd605e36586cb94

---

# Linux 用 DSC の nxScript リソース

PowerShell Desired State Configuration (DSC) の **nxScript** リソースは、Linux ノード上で Linux スクリプトを実行するためのメカニズムを備えています。

## 構文

```
nxScript <string> #ResourceName
{
    GetScript = <string>
    SetScript = <string>
    TestScript = <string>
    [ User = <string> ]
    { Group = <string> ]
    [ DependsOn = <string[]> ]

}
```

## プロパティ

|  プロパティ |  説明 | 
|---|---|
| GetScript| [Get-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521625.aspx) コマンドレットを呼び出すと実行されるスクリプトを提供します。 スクリプトは、#!/bin/bash などのシバンで開始する必要があります。| 
| SetScript| スクリプトを提供します。 [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットを呼び出すと、**TestScript** が最初に実行されます。 **TestScript** ブロックが 0 以外の終了コードを返した場合は、**SetScript** ブロックが実行されます。 **TestScript** が終了コード 0 を返した場合は、**SetScript** が実行されません。 スクリプトは、`#!/bin/bash` などのシバンで開始する必要があります。| 
| TestScript| スクリプトを提供します。 [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットを呼び出すと、このスクリプトが実行されます。 0 以外の終了コードが返された場合、SetScript が実行されます。 終了コード 0 が返された場合、**SetScript** は実行されません。 [Test-DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) コマンドレットを呼び出すと、**TestScript** も実行されます。 ただし、この場合、**TestScript** からどのような終了コードが返されるに関係なく、**SetScript** は実行されません。 実際の構成が現在の Desired State Configuration と一致する場合、**TestScript** は終了コード 0 を返す必要があります。一致しない場合は、0 以外の終了コードを返す必要があります。 (現在の Desired State Configuration は DSC を使用しているノードで適用された最後の構成です)。 スクリプトは、1#!/bin/bash.1 などのシバンで開始する必要があります。| 
| User| スクリプトを実行するユーザー。| 
| グループ| スクリプトを実行するグループ。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの **ID** が **ResourceName** で、そのタイプが **ResourceType** である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 

## 例

次の例では、追加の構成管理を実行するための **nxScript** リソースの使用を示します。

```
Import-DSCResource -Module nx 

Node $node {
nxScript KeepDirEmpty{

    GetScript = @"
#!/bin/bash
ls /tmp/mydir/ | wc -l
"@

    SetScript = @"
#!/bin/bash
rm -rf /tmp/mydir/*
"@

    TestScript = @'
#!/bin/bash
filecount=`ls /tmp/mydir | wc -l`
if [ $filecount -gt 0 ]
then
    exit 1
else
    exit 0
fi
'@
} 
}
```




<!--HONumber=Oct16_HO1-->


