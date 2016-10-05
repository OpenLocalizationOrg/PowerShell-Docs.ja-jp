---
title: "Linux 用 DSC の nxEnvironment リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 0a7ab24ff278defd7fc0a80f1dbd45bfa0e16427

---

# Linux 用 DSC の nxEnvironment リソース

PowerShell Desired State Configuration (DSC) の **nxEnvironment** リソースは、Linux ノード上でシステム環境変数を管理するためのメカニズムを備えています。

## 構文

```
nxEnvironment <string> #ResourceName
{
    Name = <string>
    [ Value = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ Path = <bool> }
    [ DependsOn = <string[]> ]

}
```

## プロパティ

|  プロパティ |  説明 | 
|---|---|
| 名前| 特定の状態を保証する環境変数の名前を示します。| 
| 値| 環境変数に割り当てる値。| 
| Ensure| 変数が存在するかどうかをチェックするかどうかを決定します。 変数が存在することを保証するには、このプロパティを "Present" に設定します。 変数が存在しないことを保証するには、"Absent" に設定します。 既定値は "Present" です。| 
| パス| 構成されている環境変数を定義します。 環境変数が **Path** 変数である場合は、このプロパティを **$true** に設定します。それ以外の場合は、**$false** に設定します。 既定値は **$false** です。 構成されている変数が **Path** 変数である場合は、**Value** プロパティによって提供される値が既存の値に追加されます。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの **ID** が **ResourceName** で、そのタイプが **ResourceType** である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 

## 追加情報

* **Path** がないか、または **$false** に設定されている場合、環境変数は、`/etc/environment` で管理されます。 プログラムまたはスクリプトに、`/etc/environment` ファイルを取得して、管理対象の環境変数にアクセスする構成が必要である場合があります。
* **Path** が **$true** に設定されている場合、環境変数はファイル `/etc/profile.d/DSCenvironment.sh` で管理されます。 このファイルが存在しない場合は作成されます。 **Ensure**が "Absent" に設定され、**Path** が **$true** に設定されている場合、既存の環境変数は `/etc/profile.d/DSCenvironment.sh` からのみ削除され、他のファイルからは削除されません。

## 例

次の例は、**nxEnvironment** リソースを使用して、**TestEnvironmentVariable** が存在し、値 "Test-Value" が指定されることを保証する方法を示しています。 **TestEnvironmentVariable** が存在しない場合は、作成されます。

```
Import-DSCResource -Module nx 


nxEnvironment EnvironmentExample
{
    Ensure = “Present”
    Name = “TestEnvironmentVariable”
    Value = “TestValue”
}
```





<!--HONumber=Oct16_HO1-->


