---
title: "DSC 環境リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 20a7711604033b5ff1484dbb526df2642a9a1738

---

# DSC 環境リソース

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

Windows PowerShell Desired State Configuration (DSC) の __Environment__ リソースは、システム環境変数を管理するためのメカニズムを備えています。

## 構文
``` mof
Environment [string] #ResourceName
{
    Name = [string]
    [ Ensure = [string] { Absent | Present }  ]
    [ Path = [bool] ]
    [ DependsOn = [string[]] ]
    [ Value = [string] ]
}
```

## プロパティ

|  プロパティ  |  説明   | 
|---|---| 
| 名前| 特定の状態を保証する環境変数の名前を示します。| 
| Ensure| 変数が存在するかどうかを示します。 環境変数が存在しない場合に作成する場合、または環境変数が既に存在する場合にその値が __Value__ プロパティによって提供される値と一致することを保証するには、このプロパティを __Present__ に設定します。 環境変数が存在する場合に削除するには、__Absent__ に設定します。| 
| パス| 構成されている環境変数を定義します。 環境変数が __Path__ 変数である場合は、このプロパティを __$true__ に設定します。それ以外の場合は、__$false__ に設定します。 既定値は __$false__ です。 構成されている変数が __Path__ 変数である場合は、__Value__ プロパティによって提供される値が既存の値に追加されます。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が __ResourceName__ で、そのタイプが __ResourceType__ である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 
| 値| 環境変数に割り当てる値。| 

## 例

次の例では、__TestEnvironmentVariable__ が存在し、その値が __TestValue__ であることを保証します。 この環境変数が存在しない場合は、作成されます。

```powershell
Environment EnvironmentExample
{
    Ensure = "Present"  # You can also set Ensure to "Absent"
    Name = "TestEnvironmentVariable"
    Value = "TestValue"
}
```




<!--HONumber=Oct16_HO1-->


