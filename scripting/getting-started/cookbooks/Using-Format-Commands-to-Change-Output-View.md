---
title: "Format コマンドを使用した出力ビューの変更"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 63515a06-a6f7-4175-a45e-a0537f4f6d05
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4d8c2392c6a9f59dc5e601308bc08db8c653760e

---

# Format コマンドを使用した出力ビューの変更
Windows PowerShell には、特定のオブジェクトのプロパティの表示を制御するためのコマンドレットのセットがあります。 すべてのコマンドレットの名前は、動詞 **Format** から始まります。 表示するプロパティを 1 つ以上選択できます。

**Format** コマンドレットとしては、**Format-Wide**、**Format-List**、**Format-Table**、および **Format-Custom** があります。 このユーザー ガイドでは、**Format-Wide**、**Format-List**、および **Format-Table** コマンドレットについてのみ説明します。

各 Format コマンドレットには、表示する特定のプロパティを指定しない場合に使用される既定のプロパティがあります。 どのコマンドレットでも、同じパラメーター名 **Property** を使用して、表示するプロパティを指定します。 **Format-Wide** はプロパティを 1 つだけ表示するため、その **Property** パラメーターには値を 1 つだけ設定します。ただし、**Format-List** と **Format-Table** の Property パラメーターには、プロパティ名のリストを使用できます。

Windows PowerShell のインスタンスを 2 つ実行してコマンド **Get-Process -Name powershell** を使用すると、次のような出力が得られます。

```
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    995       9    30308      27996   152     2.73   2760 powershell
    331       9    23284      29084   143     1.06   3448 powershell
```

このセクションの残りの部分で、このコマンドの出力の表示の仕方を **Format** コマンドレットを使用して変更する方法について解説します。

### 単一項目出力のための Format-Wide の使用
**Format-Wide** コマンドレットは、既定では、オブジェクトの既定プロパティのみ表示します。 各オブジェクトに関連付けられている情報が、1 つの列に表示されます。

```
PS> Get-Process -Name powershell | Format-Wide

powershell                              powershell
```

既定以外のプロパティを指定することもできます。

```
PS> Get-Process -Name powershell | Format-Wide -Property Id

2760                                    3448
```

#### 列による Format-Wide 表示の制御
**Format-Wide** コマンドレットでは、一度に表示できるプロパティは 1 つだけです。 これは、1 行に要素が 1 つだけ示される単純なリストを表示する場合に便利です。 単純なリスト表示にするには、**Column** パラメーターの値を 1 に設定します。次のように入力します。

```
Get-Command Format-Wide -Property Name -Column 1
```

### リスト ビューのための Format-List の使用
**Format-List** コマンドレットは、リストの形式でオブジェクトを表示します。各プロパティはラベル付けされ、別々の行に表示されます。

```
PS> Get-Process -Name powershell | Format-List

Id      : 2760
Handles : 1242
CPU     : 3.03125
Name    : powershell

Id      : 3448
Handles : 328
CPU     : 1.0625
Name    : powershell
```

必要な数だけプロパティを指定できます。

```
PS> Get-Process -Name powershell | Format-List -Property ProcessName,FileVersion
,StartTime,Id

ProcessName : powershell
FileVersion : 1.0.9567.1
StartTime   : 2006-05-24 13:42:00
Id          : 2760

ProcessName : powershell
FileVersion : 1.0.9567.1
StartTime   : 2006-05-24 13:54:28
Id          : 3448
```

#### Format-List でワイルドカードを使用して詳細情報を取得する
**Format-List** コマンドレットでは、その **Property** パラメーターの値としてワイルドカードを使用できます。 こうすることで、詳細情報を表示できます。 多くの場合、オブジェクトには必要以上の情報が含まれています。既定では Windows PowerShell がすべてのプロパティ値は表示しないのはそのためです。 すべてのオブジェクトのプロパティを表示する、 **Format-list-プロパティ \ &#42;** コマンドです。 次のコマンドは、1 つのプロセスについて 60 行を超える出力を生成します。

```
Get-Process -Name powershell | Format-List -Property *
```

**Format-List** コマンドは詳細の表示に役立ちますが、項目が多数含まれる出力の概要が必要な場合は、より単純な表形式ビューの方がしばしば好都合です。

### 表形式出力のための Format-Table の使用
プロパティ名を指定せずに **Format-Table** コマンドレットを使用して **Get-Process** コマンドの出力を書式設定した場合は、書式設定を行わない場合とまったく同じ出力になります。 その理由は、ほとんどの Windows PowerShell オブジェクトがそうであるように、プロセスは通常、表形式で表示されるからです。

```
PS> Get-Process -Name powershell | Format-Table

Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
   1488       9    31568      29460   152     3.53   2760 powershell
    332       9    23140        632   141     1.06   3448 powershell
```

#### Format-Table 出力の改善 (AutoSize)
表形式ビューは比較情報を大量に表示するには便利ですが、データの表示幅が狭すぎる場合は解釈しにくいことがあります。 たとえば、プロセス パス、ID、名前、および会社を表示しようとすると、プロセス パスと会社の列の出力が切り捨てられます。

```
PS> Get-Process -Name powershell | Format-Table -Property Path,Name,Id,Company

Path                Name                                 Id Company
----                ----                                 -- -------
C:\Program Files... powershell                         2836 Microsoft Corpor...
```

**Format-Table** コマンドを実行するときに **AutoSize** パラメーターを指定すると、Windows PowerShell は、表示される実際のデータに基づいて、列の幅を計算します。 これによって **Path** 列は読み取れるようになりますが、会社列は切り捨てられたままです。

```
PS> Get-Process -Name powershell | Format-Table -Property Path,Name,Id,Company -
AutoSize

Path                                                    Name         Id Company
----                                                    ----         -- -------
C:\Program Files\Windows PowerShell\v1.0\powershell.exe powershell 2836 Micr...
```

**Format-Table** コマンドレットはデータを切り捨てることもありますが、それが行われるのは画面の端だけです。 最後に表示されるもの以外のプロパティには、最長データ要素が正しく表示されるために必要なだけのサイズが与えられます。 **Property** 値リストで **Path** と **Company** の場所を入れ替えると、その会社名が表示されるようになりますが、パスは切り捨てられます。

```
PS> Get-Process -Name powershell | Format-Table -Property Company,Name,Id,Path -
AutoSize

Company               Name         Id Path
-------               ----         -- ----
Microsoft Corporation powershell 2836 C:\Program Files\Windows PowerShell\v1...
```

**Format-Table** コマンドは、プロパティ リストの先頭に近いプロパティほど重要であると見なします。 したがって、先頭に最も近いプロパティを完全に表示しようとします。 **Format-Table** コマンドがすべてのプロパティを表示できない場合は、表示から一部の列が削除され、警告が出されます。 この動作は、**Name** をリストの最後のプロパティにすれば、確認できます。

```
PS> Get-Process -Name powershell | Format-Table -Property Company,Path,Id,Name -
AutoSize

WARNING: column "Name" does not fit into the display and was removed.

Company               Path                                                    I
                                                                              d
-------               ----                                                    -
Microsoft Corporation C:\Program Files\Windows PowerShell\v1.0\powershell.exe 6
```

上記の出力では、リストに収まるように ID 列が切り捨てられ、列見出しが重なっています。 列の自動サイズ変更は、常に目的どおりになるとは限りません。

#### Format-Table 出力の列内の折り返し (Wrap)
**Wrap** パラメーターを使用して、長い **Format-Table** データをその表示列内で強制的に折り返すことができます。 **Wrap** パラメーターだけを使用した場合、必ずしも期待どおりにはなりません。同時に **AutoSize** も指定しなければ、既定の設定が使用されるためです。

```
PS> Get-Process -Name powershell | Format-Table -Wrap -Property Name,Id,Company,
Path

Name                                 Id Company             Path
----                                 -- -------             ----
powershell                         2836 Microsoft Corporati C:\Program Files\Wi
                                        on                  ndows PowerShell\v1
                                                            .0\powershell.exe
```

**Wrap** パラメーターを単独で使用することの利点は、処理があまり遅くならないことです。 **AutoSize** を使用した場合、大規模なディレクトリ システムの再帰的ファイル リスト表示を実行すると、最初の出力項目が表示されるまで非常に長い時間がかかり、大量のメモリが使用されることがあります。

システム負荷が気にならなければ、**AutoSize** は **Wrap** パラメーターと同時に使用した場合に効果を発揮します。 **Wrap** パラメーターなしで **AutoSize** を指定した場合と同様に、最初の各列には常に、各項目を 1 行に表示するために必要なだけの幅が割り当てられます。 唯一の違いは、必要に応じて最終的な列が折り返されることです。

```
PS> Get-Process -Name powershell | Format-Table -Wrap -AutoSize -Property Name,I
d,Company,Path

Name         Id Company               Path
----         -- -------               ----
powershell 2836 Microsoft Corporation C:\Program Files\Windows PowerShell\v1.0\
                                      powershell.exe
```

最大幅の列を最初に指定すると、一部の列が表示されない場合があります。したがって、最小のデータ要素を最初に指定することが最も安全です。 次の例では、極端に幅をとるパス要素を最初に指定しています。折り返しも使用していますが、最後の **Name** 列が失われています。

```
PS> Get-Process -Name powershell | Format-Table -Wrap -AutoSize -Property Path,I
d,Company,Name

WARNING: column "Name" does not fit into the display and was removed.

Path                                                      Id Company
----                                                      -- -------
C:\Program Files\Windows PowerShell\v1.0\powershell.exe 2836 Microsoft Corporat
                                                             ion
```

#### 表出力の整理 (-GroupBy)
表形式出力制御のためのもう一つの便利なパラメーターは、**GroupBy** です。 長い表形式リストは特に、比較しにくい場合があります。 **GroupBy** パラメーターは、プロパティ値に基づいて出力をグループ化します。 たとえば、検査しやすくするために、プロセスを会社別にグループ化できます。この場合、会社の値をプロパティ リストに入れません。

```
PS> Get-Process -Name powershell | Format-Table -Wrap -AutoSize -Property Name,I
d,Path -GroupBy Company

   Company: Microsoft Corporation

Name         Id Path
----         -- ----
powershell 1956 C:\Program Files\Windows PowerShell\v1.0\powershell.exe
powershell 2656 C:\Program Files\Windows PowerShell\v1.0\powershell.exe
```




<!--HONumber=Oct16_HO1-->


