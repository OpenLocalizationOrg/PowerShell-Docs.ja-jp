---
title: "Linux 用 DSC の nxFileLine リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 9196129e79272d8bee717ef8a5d42fb590760a0f

---

# Linux 用 DSC の nxFileLine リソース

PowerShell Desired State Configuration (DSC) の **nxFileLine** リソースは、Linux ノード上で構成ファイルの行を管理するためのメカニズムを備えています。

## 構文

```
nxFileLine <string> #ResourceName
{
    FilePath = <string>
    ContainsLine = <string>
    [ DoesNotContainPattern = <string> ]
    [ DependsOn = <string[]> ]

}
```

## プロパティ

|  プロパティ |  説明 | 
|---|---|
| ファイル パス| ターゲット ノード上の行を管理するファイルの完全パス。| 
| ContainsLine| ファイルに行が存在するようにします。 ファイルに行が存在しない場合、この行がファイルに追加されます。 **ContainsLine** は必須ですが、必要ない場合は空の文字列 (`ContainsLine = ‘’``) に設定することができます。| 
| DoesNotContainPattern| ファイルに存在することができない行の正規表現パターン。 ファイルに存在する行のうち、この正規表現に一致する行は、ファイルから削除されます。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの **ID** が **ResourceName** で、そのタイプが **ResourceType** である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 

## 例

この例では、**nxFileLine** リソースを使用して、ユーザー monuser が not requiretty に構成されるように `/etc/sudoers` ファイルを構成しています。

```
Import-DSCResource -Module nx 

nxFileLine DoNotRequireTTY
{
   FilePath = “/etc/sudoers”
   ContainsLine = 'Defaults:monuser !requiretty'
   DoesNotContainPattern = "Defaults:monuser[ ]+requiretty"
} 
```




<!--HONumber=Oct16_HO1-->


