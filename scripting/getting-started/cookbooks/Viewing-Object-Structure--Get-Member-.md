---
title: "オブジェクトの構造の表示 (Get-member)"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a1819ed2-2ef3-453a-b2b0-f3589c550481
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: acd52c1271013320dafa7f7bf5c72b752e07574b

---

# オブジェクトの構造を表示する (Get-member)
オブジェクトは、Windows PowerShell において非常に中心的な役割を担っているため、任意のオブジェクトの種類で動作するよう設計されたネイティブ コマンドがいくつかあります。 最も重要なコマンドは、**Get-Member** コマンドです。

コマンドで返されるオブジェクトを分析するための最も単純な手法は、パイプを使用してそのコマンドの出力を **Get-Member** コマンドレットに渡すことです。 **Get-Member** コマンドレットでは、オブジェクトの種類の正式な名前とそのメンバーの完全な一覧が表示されます。 返される要素の数が非常に多いことがあります。 たとえば、プロセス オブジェクトには 100 を超えるメンバーが含まれることがあります。

プロセス オブジェクトのすべてのメンバーを表示して、出力をすべて確認できるようにページングするには、次のように入力します。

```
PS> Get-Process | Get-Member | Out-Host -Paging
```

このコマンドの出力は次のようになります。

```
TypeName: System.Diagnostics.Process

Name                           MemberType     Definition
----                           ----------     ----------
Handles                        AliasProperty  Handles = Handlecount
Name                           AliasProperty  Name = ProcessName
NPM                            AliasProperty  NPM = NonpagedSystemMemorySize
PM                             AliasProperty  PM = PagedMemorySize
VM                             AliasProperty  VM = VirtualMemorySize
WS                             AliasProperty  WS = WorkingSet
add_Disposed                   Method         System.Void add_Disposed(Event...
...
```

表示する要素をフィルター処理することで、この長い情報の一覧をより使いやすくできます。 **Get-Member** コマンドを使用すると、プロパティであるメンバーのみを一覧表示できます。 プロパティにはいくつかの形式があります。 **Get-MemberMemberType** パラメーターを値 **Properties** に設定すると、コマンドレットはすべての種類のプロパティを表示します。 結果の一覧はこれでもまだ非常に長いですが、より管理しやすくなります。

```
PS> Get-Process | Get-Member -MemberType Properties

   TypeName: System.Diagnostics.Process

Name                       MemberType     Definition
----                       ----------     ----------
Handles                    AliasProperty  Handles = Handlecount
Name                       AliasProperty  Name = ProcessName
...
ExitCode                   Property       System.Int32 ExitCode {get;}
...
Handle                     Property       System.IntPtr Handle {get;}
...
CPU                        ScriptProperty System.Object CPU {get=$this.Total...
...
Path                       ScriptProperty System.Object Path {get=$this.Main...
...
```

> [!NOTE]
> MemberType の指定できる値は、AliasProperty、CodeProperty、Property、NoteProperty、ScriptProperty、Properties、PropertySet、Method、CodeMethod、ScriptMethod、Methods、ParameterizedProperty、MemberSet、および All です。

プロセスには、60 を超えるプロパティがあります。 Windows PowerShell では、既知のオブジェクトのほんの一部のプロパティしか表示されません。これは、すべてを表示すると管理が困難なほどの大量の情報が生成されるためです。

> [!NOTE]
> Windows PowerShell は、.format.ps1xml で終わる名前を持つ XML ファイルに保存されている情報を使用して、オブジェクトの種類を表示する方法を決定します。 プロセス オブジェクトの書式設定データは、.NET System.Diagnostics.Process オブジェクトであり、PowerShellCore.format.ps1xml に保存されます。

Windows PowerShell が既定で表示する以外のプロパティを確認する必要がある場合は、出力データを自分で書式設定する必要があります。 これは、書式設定コマンドレットを使用して行えます。




<!--HONumber=Oct16_HO1-->


