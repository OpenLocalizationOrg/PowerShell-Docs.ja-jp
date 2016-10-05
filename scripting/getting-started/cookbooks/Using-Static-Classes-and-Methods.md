---
title: "静的なクラスとメソッドの使用"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 418ad766-afa6-4b8c-9a44-471889af7fd9
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 28bc665c3ffb1b74a2ff922584c31a8657842a0f

---

# 静的なクラスとメソッドの使用
.NET Framework のクラスの中には、**New-Object** では作成できないものもあります。 たとえば、**New-Object** で **System.Environment** オブジェクトや **System.Math** オブジェクトを作成しようとすると、次のようなエラー メッセージが表示されます。

```
PS> New-Object System.Environment
New-Object : Constructor not found. Cannot find an appropriate constructor for
type System.Environment.
At line:1 char:11
+ New-Object  <<<< System.Environment
PS> New-Object System.Math
New-Object : Constructor not found. Cannot find an appropriate constructor for
type System.Math.
At line:1 char:11
+ New-Object  <<<< System.Math
```

エラーが発生する理由は、これらのクラスからは、新しいオブジェクトを作成することができないためです。 これらのクラスは、メソッドおよびプロパティが収められている参照用のライブラリであり、状態の変化を伴いません。 これらのメソッドやプロパティは、オブジェクトを作成しなくても使用できます。 これらのクラスやメソッドは、作成、破棄、変更されないため、*静的クラス*と呼ばれます。 この点をわかりやすく説明するために、実際に静的クラスを使用する例を紹介します。

### System.Environment による環境データの取得
通常、Windows PowerShell でオブジェクトを操作するために最初に行うことは、Get-Member を使用して、そのオブジェクトに含まれているメンバーを調べることです。 静的クラスの場合、実際のクラスがオブジェクトではないため、このプロセスが若干異なります。

#### 静的クラス System.Environment の参照
静的クラスを参照するには、そのクラス名を角かっこで囲みます。 たとえば、**System.Environment** を参照するには、角かっこの内側に名前を入力します。 これにより、型に関する一般的な情報が表示されます。

```
PS> [System.Environment]

IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     False    Environment                              System.Object
```

> [!NOTE]
> 既に説明したように、**New-Object** を使用するとき、 型名の前には ’**System.**’ が自動的に追加されます。 角かっこで囲まれた型名を使用する場合も同様です。つまり、**[System.Environment]** は、**[Environment]** と指定することもできます。

**System.Environment** クラスには、現在のプロセス (Windows PowerShell 内で作業している場合は powershell.exe) の作業環境に関する一般情報が格納されます。

このクラスの詳細を表示するには、入力しようとするかどうかは **\[System.Environment] |Get-member**, 、オブジェクトの種類があるという報告 **System.RuntimeType** , ではなく、 **System.Environment**:

```
PS> [System.Environment] | Get-Member

   TypeName: System.RuntimeType
```

Get-Member で静的メンバーを表示するには、**Static** パラメーターを指定します。

```
PS> [System.Environment] | Get-Member -Static

   TypeName: System.Environment

Name                       MemberType Definition
----                       ---------- ----------
Equals                     Method     static System.Boolean Equals(Object ob...
Exit                       Method     static System.Void Exit(Int32 exitCode)
...
CommandLine                Property   static System.String CommandLine {get;}
CurrentDirectory           Property   static System.String CurrentDirectory ...
ExitCode                   Property   static System.Int32 ExitCode {get;set;}
HasShutdownStarted         Property   static System.Boolean HasShutdownStart...
MachineName                Property   static System.String MachineName {get;}
NewLine                    Property   static System.String NewLine {get;}
OSVersion                  Property   static System.OperatingSystem OSVersio...
ProcessorCount             Property   static System.Int32 ProcessorCount {get;}
StackTrace                 Property   static System.String StackTrace {get;}
SystemDirectory            Property   static System.String SystemDirectory {...
TickCount                  Property   static System.Int32 TickCount {get;}
UserDomainName             Property   static System.String UserDomainName {g...
UserInteractive            Property   static System.Boolean UserInteractive ...
UserName                   Property   static System.String UserName {get;}
Version                    Property   static System.Version Version {get;}
WorkingSet                 Property   static System.Int64 WorkingSet {get;}
TickCount                               ExitCode
```

これで、System.Environment から、必要なプロパティを選んで表示できます。

#### System.Environment の静的プロパティの表示
System.Environment の場合はプロパティも静的です。通常のプロパティとは異なる方法で指定する必要があります。 操作の対象が静的メソッドまたは静的プロパティであることを Windows PowerShell に伝えるには **::** を使用します。 Windows PowerShell の起動に使ったコマンドを表示するには、次のように入力して、**CommandLine** プロパティを確認します。

```
PS> [System.Environment]::Commandline
"C:\Program Files\Windows PowerShell\v1.0\powershell.exe"
```

オペレーティング システムのバージョンを確認するには、次のように入力して、OSVersion プロパティを表示します。

```
PS> [System.Environment]::OSVersion

           Platform ServicePack         Version             VersionString
           -------- -----------         -------             -------------
            Win32NT Service Pack 2      5.1.2600.131072     Microsoft Windows...
```

コンピューターがシャットダウンの処理中であるかどうかを確認するには、**HasShutdownStarted** プロパティを表示します。

```
PS> [System.Environment]::HasShutdownStarted
False
```

### System.Math による数学的演算の実行
System.Math 静的クラスは、数学的演算を行うのに役立ちます。 **System.Math** の重要なメンバーのほとんどはメソッドであり、**Get-Member** を使って表示できます。

> [!NOTE]
> System.Math には同じ名前の複数のメソッドが存在しますが、これらはパラメーターの型が異なります。

**System.Math** クラスのメソッドを一覧表示するには、次のコマンドを入力します。

```
PS> [System.Math] | Get-Member -Static -MemberType Methods

   TypeName: System.Math

Name            MemberType Definition
----            ---------- ----------
Abs             Method     static System.Single Abs(Single value), static Sy...
Acos            Method     static System.Double Acos(Double d)
Asin            Method     static System.Double Asin(Double d)
Atan            Method     static System.Double Atan(Double d)
Atan2           Method     static System.Double Atan2(Double y, Double x)
BigMul          Method     static System.Int64 BigMul(Int32 a, Int32 b)
Ceiling         Method     static System.Double Ceiling(Double a), static Sy...
Cos             Method     static System.Double Cos(Double d)
Cosh            Method     static System.Double Cosh(Double value)
DivRem          Method     static System.Int32 DivRem(Int32 a, Int32 b, Int3...
Equals          Method     static System.Boolean Equals(Object objA, Object ...
Exp             Method     static System.Double Exp(Double d)
Floor           Method     static System.Double Floor(Double d), static Syst...
IEEERemainder   Method     static System.Double IEEERemainder(Double x, Doub...
Log             Method     static System.Double Log(Double d), static System...
Log10           Method     static System.Double Log10(Double d)
Max             Method     static System.SByte Max(SByte val1, SByte val2), ...
Min             Method     static System.SByte Min(SByte val1, SByte val2), ...
Pow             Method     static System.Double Pow(Double x, Double y)
ReferenceEquals Method     static System.Boolean ReferenceEquals(Object objA...
Round           Method     static System.Double Round(Double a), static Syst...
Sign            Method     static System.Int32 Sign(SByte value), static Sys...
Sin             Method     static System.Double Sin(Double a)
Sinh            Method     static System.Double Sinh(Double value)
Sqrt            Method     static System.Double Sqrt(Double d)
Tan             Method     static System.Double Tan(Double a)
Tanh            Method     static System.Double Tanh(Double value)
Truncate        Method     static System.Decimal Truncate(Decimal d), static...
```

これにより、いくつかの数学的メソッドが表示されます。 以下は、いくつかの一般的なメソッドを実行した結果を示すコマンドの一覧です。

```
PS> [System.Math]::Sqrt(9)
3
PS> [System.Math]::Pow(2,3)
8
PS> [System.Math]::Floor(3.3)
3
PS> [System.Math]::Floor(-3.3)
-4
PS> [System.Math]::Ceiling(3.3)
4
PS> [System.Math]::Ceiling(-3.3)
-3
PS> [System.Math]::Max(2,7)
7
PS> [System.Math]::Min(2,7)
2
PS> [System.Math]::Truncate(9.3)
9
PS> [System.Math]::Truncate(-9.3)
-9
```




<!--HONumber=Oct16_HO1-->


