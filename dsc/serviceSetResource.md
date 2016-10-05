---
title: "DSC ServiceSet リソース"
ms.date: 2016-05-23
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 871d697626a0376e8f1f27bdbbf16d8612a56a79

---

# DSC ServiceSet リソース

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0


PowerShell Desired State Configuration (DSC) の **ServiceSet** リソースは、ターゲット ノード上でサービスを管理するためのメカニズムを備えています。 このリソースは[複合リソース](authoringResourceComposite.md)であり、`Name` プロパティに指定されているサービスごとに [Service リソース](serviceResource.md)を呼び出します。

サービスの数を同じ状態に構成するときにこのリソースを使用します。

## 構文

```
Service [string] #ResourceName
{
    Name = [string[]]
    [ StartupType = [string] { Automatic | Disabled | Manual }  ]
    [ BuiltInAccount = [string] { LocalService | LocalSystem | NetworkService }  ]
    [ State = [string] { Running | Stopped }  ]
    [ Ensure = [string] { Absent | Present }  ]
    [ Credential = [PSCredential] ]
    [ DependsOn = [string[]] ]
    
}
```

## プロパティ

|  プロパティ  |  説明   | 
|---|---| 
| 名前| サービス名を示します。 これは表示名とは異なることがあります。 [Get-Service](https://technet.microsoft.com/en-us/library/hh849804.aspx) コマンドレットを使用すると、サービスとその現在の状態の一覧を取得できます。|
| StartupType| サービスのスタートアップの種類を示します。 このプロパティに許容される値は、**Automatic**、**Disabled**、および **Manual** です。|  
| BuiltInAccount| サービスに使用するサインイン アカウントを示します。 このプロパティに許容される値は、**LocalService**、**LocalSystem**、および **NetworkService** です。| 
| State| サービスの状態 (**停止**または**実行中**) を示します。| 
| Ensure| サービスがシステムに存在するかどうかを示します。 サービスが存在しないようにするには、このプロパティを **[Absent]** に設定します。 ターゲットが存在するようにするには、**[Present]** (既定値) に設定します。|
| Credential| サービス リソースを実行するアカウントの資格情報を示します。 このプロパティおよび **BuiltinAccount** プロパティを同時に使用することはできません。| 
| DependsOn| このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が *ResourceName* で、そのタイプが *ResourceType* である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 



## 例

次の構成で "Windows オーディオ" サービスと "リモート デスクトップ サービス" サービスが開始します。

```powershell
configuration ServiceSetTest
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    Node localhost
    {

        ServiceSet ServiceSetExample
        {
            Name        = @("TermService", "Audiosrv")
            StartupType = "Manual"
            State       = "Running"
        } 
    }
}
```




<!--HONumber=Oct16_HO1-->


