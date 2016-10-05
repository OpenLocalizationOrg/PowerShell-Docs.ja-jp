---
title: "PowerShell Desired State Configuration の概要"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: c5ee7f7e7678b60700edb1ab1b66139791ea67c6

---

# PowerShell Desired State Configuration の概要 #

このガイドでは、PowerShell Desired State Configuration ドキュメントの作成を開始し、マシンに適用する方法について説明します。 PowerShell コマンドレット、モジュール、および関数の基礎知識があることを前提とします。 


## 構成の作成 ##

[**構成**](https://msdn.microsoft.com/en-us/powershell/dsc/configurations)は、環境について説明するドキュメントです。 環境は "**ノード**" で構成され、ノードは通常、仮想マシンまたは物理マシンです。 

構成には、さまざまな形式があります。 新しい構成を作成する最も簡単な方法は、.ps1 (PowerShell スクリプト) ファイルを作成することです。 これを行うには、任意のエディターを開きます。 PowerShell ISE では DSC がネイティブに認識されるため、適切な選択です。 以下を PS1 として保存します。

```powershell
configuration MyFirstConfiguration
{
    Import-DscResource -Name WindowsFeature

    Node localhost
    {
        WindowsFeature IIS
        {
            Name = "IIS"

        }
        
    }

}
```
## 構成のパーツ ##
**Configuration** は、PowerShell 4.0 に追加されたキーワードです。 これは、Desired State Configuration で使用される特別な種類の PowerShell 関数を示します。 この例では、関数の名前は myFirstConfiguration です。 

次の行は、モジュールのインポートと同様のインポート ステートメントです。 これについては後で説明します。

"Node" では、この構成が機能するマシン名を定義します。 この構成はローカルで編集されますが、構成はリモート ノードに到達し、それらを構成できます。 

ノードには、マシン名または IP アドレスを指定できます。 1 つの構成ドキュメントに複数のノードを指定できます。 [構成データ](https://msdn.microsoft.com/en-us/powershell/dsc/configdata)を使用して、同じ構成を複数のノードに適用することもできます。 この場合、ノードは "localhost" であり、ローカル コンピューターを意味します。 

次の項目は、[**リソース**](https://msdn.microsoft.com/en-us/powershell/dsc/resources)です。 リソースは、構成の構成要素です。 各リソースは、マシンの 1 つの側面の実装ロジックを定義するモジュールです。 PowerShell で **Get-DscResource** を実行して、コンピューター上のすべてのリソースを表示できます。 この構成の 2 行目にある **Import-DscResource** を含む構成で使用するには、リソースがローカル コンピューター上に存在し、インポートされている必要があります。 

**構成の施行**

上記のスクリプトを保存して実行した場合、出力は生成されません。 これは、構成は単なる関数であり、上記のスクリプトでは関数を定義してもまだ実行されていないためです。 関数を定義した後、呼び出す必要があります。
```powershell
myFirstConfiguration
```

実行すると、構成関数により構成が有効であることが検証されます。 構成に構文エラーがなく、リソースにすべての必須パラメーターが定義され、実行前にすべてのリソースがインポートされている必要があります。

構成を実行すると、構成内の各ノードに対して **.MOF ファイル**を含む構成の名前が付いたフォルダーが作成されます。 .MOF ファイルは、ネットワーク経由で通信するために PowerShell DSC で使用される標準ベースの管理形式です。

構成を適用するには:
```powershell
Start-DscConfiguration -Path ./myFirstConfiguration
```
これにより、構成内のノードに到達してそれらを構成する PowerShell ジョブが作成されます。 ジョブの出力を表示するには、-Wait を使用します。 
```powershell
Start-DscConfiguration -Path ./myFirstConfiguration -Wait
```




<!--HONumber=Oct16_HO1-->


