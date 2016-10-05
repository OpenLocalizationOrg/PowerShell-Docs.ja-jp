---
title: "Linux 用 DSC の nxService リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 3835495705297616a41329bcfdaad42b464115d8

---

# Linux 用 DSC の nxService リソース

PowerShell Desired State Configuration (DSC) の **nxService** リソースは、Linux ノード上でサービスを管理するためのメカニズムを備えています。

## 構文

```
nxService <string> #ResourceName
{
    Name = <string>
    [ Controller = <string> { init | upstart | system }  ]
    [ Enabled = <bool> ]
    [ State = <string> { Running | Stopped } ]
    [ DependsOn = <string[]> ]

}
```

## プロパティ
|  プロパティ |  説明 | 
|---|---|
| 名前| 構成するサービス/デーモンの名前。| 
| コントローラー| サービスを構成するときに使用するサービス コントローラーの種類。| 
| Enabled| ブート時にサービスを開始するかどうかを示します。| 
| State| サービスが実行されるかどうかを示します。 サービスが実行されないようにするには、このプロパティを "Stopped" に設定します。 サービスが実行されないようにするには、このプロパティを "Running" に設定します。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの **ID** が **ResourceName** で、そのタイプが **ResourceType** である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 


## 追加情報

**nxService** リソースでは、サービス定義やサービスのスクリプトが存在しない場合、これらは作成されません。 PowerShell Desired State Configuration の **nxFile** リソースを使用して、サービス定義ファイルやスクリプトの存在または内容を管理できます。

## 例

次の例では、**SystemD** サービス コントローラーを使用して登録された "httpd" サービス (Apache HTTP Server の場合) の構成を示します。

```
Import-DSCResource -Module nx 

Node $node {
#Apache Service
nxService ApacheService 
{
Name = "httpd"
State = "running"
Enabled = $true
Controller = "systemd"
}
}
```




<!--HONumber=Oct16_HO1-->


