---
title: "ノードの相互依存関係の指定"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: f4c64d3603fd01f29a63f8b180057714bf3d1197

---

# ノードの相互依存関係の指定

> 適用先: Windows PowerShell 5.0

DSC には、**WaitForAll**、**WaitForAny**、**WaitForSome** などの特別なリソースが用意されています。このリソースを構成で使用すると、他のノードの構成への依存関係を指定することができます。 これらのリソースの動作は次のとおりです。

* **WaitForAll**: **NodeName** プロパティで定義されているすべてのターゲット ノードで、指定されたリソースが目的の状態である場合に成功します。
* **WaitForAny**: **NodeName** プロパティで定義されているターゲット ノードの少なくとも 1 つで、指定されたリソースが目的の状態である場合に成功します。
* **WaitForSome**: **NodeName** プロパティのほか、**NodeCount** プロパティも指定します。 リソースは、**NodeName** プロパティで定義されたノードの最小数 (**NodeCount** で指定) で目的の状態になった場合に成功します。 

## WaitForXXXX リソースの使用

**WaitForXXXX** リソースを使用するには、待機する DSC リソースとノードを指定する、そのリソースの種類のリソース ブロックを作成します。 その後、構成の他のリソース ブロックで **DependsOn** プロパティを使用して、**WaitForXXXX** ノードで指定されている条件が成功するのを待ちます。

たとえば、次の構成では、ターゲット ノードは **MyDC** ノード上の **xADDomain** リソースが完了するまで 15 秒間隔で最大 30 回試行しながら待機した後、ドメインに参加できるようになります。

```PowerShell
Configuration JoinDomain

{
    Import-DscResource -Module xComputerManagement

    Node myDomainJoinedServer
    {

        WaitForAll DC
        {
            ResourceName      = '[xADDomain]NewDomain'
            NodeName          = 'MyDC'
            RetryIntervalSec  = 15
            RetryCount        = 30
        }

        xComputer JoinDomain
        {
            Name             = 'MyPC'
            DomainName       = 'Contoso.com'
            Credential       = (Get-Credential)
            DependsOn        ='[WaitForAll]DC'
        }
    }
}
```

>**注:** 既定では、WaitForXXX リソースは 1 回試行してから失敗します。 これは必須ではありませんが、通常は、再試行の間隔と回数を指定することをお勧めします。

## 参照
* [DSC 構成](configurations.md)
* [DSC リソース](resources.md)
* [ローカル構成マネージャーの構成](metaConfig.md)




<!--HONumber=Oct16_HO1-->


