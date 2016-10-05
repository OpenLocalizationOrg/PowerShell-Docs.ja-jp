---
title: "DSC Registry リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 48b68a99baa489dad38e7072b171db10ee0f7386

---

# DSC Registry リソース

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

Windows PowerShell Desired State Configuration (DSC) の **Registry** リソースは、ターゲット ノードでレジストリ キーと値を管理するためのメカニズムを備えています。

## 構文

```
Registry [string] #ResourceName
{
    Key = [string]
    ValueName = [string]
    [ Ensure = [string] { Absent | Present }  ]
    [ Force =  [bool]   ]
    [ Hex = [bool] ]
    [ DependsOn = [string[]] ]
    [ ValueData = [string[]] ]
    [ ValueType = [string] { Binary | Dword | ExpandString | MultiString | Qword | String }  ]
}
```

## プロパティ
|  プロパティ  |  説明   | 
|---|---| 
| キー| 特定の状態を確認するレジストリ キーのパスを示します。 このパスには、ハイブを含める必要があります。| 
| ValueName| レジストリ値の名前を示します。| 
| Ensure| キーと値が存在するかどうかを示します。 それらが存在することを保証するには、このプロパティを "Present" に設定します。 それらが存在しないことを保証するには、このプロパティを "Absent" に設定します。 既定値は "Present" です。| 
| Force| 指定のレジストリ キーが存在する場合、__Force__ はそのキーを新しい値で上書きします。| 
| Hex| 16 進形式でデータを表現するかどうかを示します。 指定した場合、DWORD/QWORD 値データが 16 進形式で表示されます。 その他の種類に対しては無効です。 既定値は __$false__ です。| 
| DependsOn| このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が __ResourceName__ で、そのタイプが __ResourceType__ である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 
| ValueData| レジストリ値のデータ。| 
| ValueType| 値の種類を示します。 サポートされている型は次のとおりです。 
<ul><li>文字列 (REG_SZ)</li>


<li>バイナリ (REG-BINARY)</li>


<li>Dword 32 ビット (REG_DWORD)</li>


<li>Qword 64 ビット (REG_QWORD)</li>


<li>複数行文字列 (REG_MULTI_SZ)</li>


<li>展開可能な文字列 (REG_EXPAND_SZ)</li></ul>

## 例
この例では、"ExampleKey"という名前のキーにあることにより、 **HKEY\_LOCAL\_MACHINE** hive します。
```powershell
Configuration RegistryTest
{
    Registry RegistryExample
    {
        Ensure      = "Present"  # You can also set Ensure to "Absent"
        Key         = "HKEY_LOCAL_MACHINE\SOFTWARE\ExampleKey"
        ValueName   = "TestValue"
        ValueData   = "TestData"
    }
}
```

>**注:** でレジストリ設定の変更、 **hkey \_current\_user** hive では、システムではなくユーザーの資格情報で構成を実行する必要があります。
>**PsDscRunAsCredential** プロパティを使用すれば、構成に対してユーザー資格情報を指定できます。 例については、「[ユーザーの資格情報を指定して DSC を実行する](runAsUser.md)」を参照してください。






<!--HONumber=Oct16_HO1-->


