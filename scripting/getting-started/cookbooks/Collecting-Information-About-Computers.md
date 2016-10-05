---
title: "コンピューターに関する情報の収集"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 9e7b6a2d-34f7-4731-a92c-8b3382eb51bb
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 96204a0ce674cacd5b830f9f8b820ce3e1cbbc20

---

# コンピューターに関する情報の収集
**Get-WmiObject** は、システム全般の管理タスクを行うための最も重要なコマンドレットです。 サブシステムの重要なすべての設定は、WMI を介して公開されています。 さらに、WMI では、データは 1 つまたは複数の項目が集まったオブジェクトとして扱われます。 Windows PowerShell の操作の対象もオブジェクトです。パイプラインを使用することにより、1 つまたは複数のオブジェクトを同じ方法で処理できます。このため、一般的な WMI アクセスにより、高度なタスクを最小限の労力で実行できるようになります。

以降、任意のコンピューターに対して **Get-WmiObject** を使用し、特定の情報を収集する例を紹介します。 **ComputerName** パラメーターの値には、ローカル コンピューターを表すドット (**.**) を指定しています。 WMI 経由でアクセス可能なコンピューターであれば、任意のコンピューターの名前または IP アドレスを指定できます。 ローカル コンピューターに関する情報を取得する場合、**-ComputerName** は省略することもできます。

### デスクトップの設定を一覧表示する
まず、ローカル コンピューターのデスクトップに関する情報を収集するコマンドについて説明します。

```
Get-WmiObject -Class Win32_Desktop -ComputerName .
```

このコマンドを実行すると、使用中であるかどうかに関係なく、すべてのデスクトップの情報が返されます。

> [!NOTE]
> WMI のクラスによっては、非常に詳しい情報が返されます。その WMI クラスのメタデータが含まれる場合もあります。 こうしたメタデータのプロパティには、通常、二重アンダースコアで始まる名前が付いているため、Select-Object を使用することでこれらのプロパティをフィルター処理できます。 アルファベット文字で始まるプロパティだけを指定するには、Property 値として **[a-z]*** を使用します。次にその例を示します。 たとえば、次のように入力します。

```
Get-WmiObject -Class Win32_Desktop -ComputerName . | Select-Object -Property [a-z]*
```

メタデータをフィルターで除外するには、パイプライン演算子 (|) を使用して、Get-WmiObject コマンドの結果を **Select-Object -Property [a-z]*** に渡します。

### BIOS 情報を一覧表示する
WMI Win32_BIOS クラスは、ローカル コンピューターのシステム BIOS について、詳細な情報を凝縮して返します。

```
Get-WmiObject -Class Win32_BIOS -ComputerName .
```

### プロセッサ情報を一覧表示する
WMI の **Win32_Processor** クラスを使用すると (通常はフィルター処理を併用)、プロセッサ全般に関する情報を取得できます。

```
Get-WmiObject -Class Win32_Processor -ComputerName . | Select-Object -Property [a-z]*
```

**SystemType** プロパティを返すことで、プロセッサ ファミリの一般的な説明を表示できます。

```
PS> Get-WmiObject -Class Win32_ComputerSystem -ComputerName . | Select-Object -Property SystemType
SystemType
----------
X86-based PC
```

### コンピューターの製造元および型番を一覧表示する
コンピューターの型番情報も **Win32_ComputerSystem** から取得できます。 OEM データを取得するのであれば、標準の表示出力で十分です。フィルター処理は不要です。

```
PS> Get-WmiObject -Class Win32_ComputerSystem
Domain              : WORKGROUP
Manufacturer        : Compaq Presario 06
Model               : DA243A-ABA 6415cl NA910
Name                : MyPC
PrimaryOwnerName    : Jane Doe
TotalPhysicalMemory : 804765696
```

このようなコマンドからの出力結果はハードウェアから直接情報が取得されますが、詳しい情報は含まれていません。 ハードウェアの製造元によって正しく構成されていないために利用できない情報もあります。

### インストールされている修正プログラムを一覧表示する
インストールされているすべての修正プログラムを一覧表示するには **Win32_QuickFixEngineering** を使用します。

```
Get-WmiObject -Class Win32_QuickFixEngineering -ComputerName .
```

このクラスからは、次のような修正プログラムの一覧が返されます。

```
Description         : Update for Windows XP (KB910437)
FixComments         : Update
HotFixID            : KB910437
Install Date        :
InstalledBy         : Administrator
InstalledOn         : 12/16/2005
Name                :
ServicePackInEffect : SP3
Status              :
```

より簡潔な出力を得るには、いくつかのプロパティを除外します。 **Get-WmiObject Property** パラメーターを使用して **HotFixID** だけを選ぶこともできますが、その場合は非常に多くの情報が返されます。既定では、すべてのメタデータが表示されるためです。

```
PS> Get-WmiObject -Class Win32_QuickFixEngineering -ComputerName . -Property HotFixID
HotFixID         : KB910437
__GENUS          : 2
__CLASS          : Win32_QuickFixEngineering
__SUPERCLASS     :
__DYNASTY        :
__RELPATH        :
__PROPERTY_COUNT : 1
__DERIVATION     : {}
__SERVER         :
__NAMESPACE      :
__PATH           :
```

**Get-WmiObject** の Property パラメーターによって制限されるのは、WMI クラスのインスタンスから返されるプロパティです。Windows PowerShell に返されるオブジェクトが制限されるわけではありません。実際、上の例を見ると、余分なデータが返されていることがわかります。 出力結果を減らすためには、**Select-Object** を使用します。

```
PS> Get-WmiObject -Class Win32_QuickFixEngineering -ComputerName . -Property Hot
FixId | Select-Object -Property HotFixId
HotFixId
--------
KB910437
```

### オペレーティング システムのバージョン情報を一覧表示する
**Win32_OperatingSystem** クラスのプロパティには、バージョンおよびサービス パックの情報が含まれます。 これらのプロパティだけを明示的に選ぶことによって、バージョン情報の概要を **Win32_OperatingSystem** から取得できます。

```
Get-WmiObject -Class Win32_OperatingSystem -ComputerName . | Select-Object -Property BuildNumber,BuildType,OSType,ServicePackMajorVersion,ServicePackMinorVersion
```

**Select-Object Property** パラメーターでは、ワイルドカードも使用できます。 ここでは **Build** または **ServicePack** で始まるすべてのプロパティを使用することが重要であるため、次のように短く記述することもできます。

```
PS> Get-WmiObject -Class Win32_OperatingSystem -ComputerName . | Select-Object -Property Build*,OSType,ServicePack*

BuildNumber             : 2600
BuildType               : Uniprocessor Free
OSType                  : 18
ServicePackMajorVersion : 2
ServicePackMinorVersion : 0
```

### ローカル ユーザーと所有者を一覧表示する
ライセンスされたユーザー数、現在のユーザー数、所有者名など、ローカル ユーザーの全般的な情報は、**Win32_OperatingSystem** のプロパティを選択することによって取得できます。 表示するプロパティを明示的に選ぶには、次のように入力します。

```
Get-WmiObject -Class Win32_OperatingSystem -ComputerName . | Select-Object -Property NumberOfLicensedUsers,NumberOfUsers,RegisteredUser
```

これをワイルドカードで簡潔に記述するには、次のように入力します。

```
Get-WmiObject -Class Win32_OperatingSystem -ComputerName . | Select-Object -Property *user*
```

### 使用可能なディスク領域を取得する
ローカル ドライブのディスク容量と空き領域を表示するには、WMI Win32_LogicalDisk クラスを使用します。 表示する必要があるのは、DriveType が 3 (WMI が固定ハード ディスクに使用する値) のドライブだけです。

```
Get-WmiObject -Class Win32_LogicalDisk -Filter "DriveType=3" -ComputerName .

DeviceID     : C:
DriveType    : 3
ProviderName :
FreeSpace    : 65541357568
Size         : 203912880128
VolumeName   : Local Disk

DeviceID     : Q:
DriveType    : 3
ProviderName :
FreeSpace    : 44298250240
Size         : 122934034432
VolumeName   : New Volume

Get-WmiObject -Class Win32_LogicalDisk -Filter "DriveType=3" -ComputerName . | Measure-Object -Property FreeSpace,Size -Sum | Select-Object -Property Property,Sum
```

### ログオン セッション情報を取得する
ユーザーに関連付けられたログオン セッションの全般的な情報は、WMI Win32_LogonSession クラスを使って取得できます。

```
Get-WmiObject -Class Win32_LogonSession -ComputerName .
```

### コンピューターにログオンしたユーザーを取得する
特定のコンピューター システムにログオンしているユーザーを表示するには、Win32_ComputerSystem を使用します。 このコマンドで返されるのは、システムのデスクトップにログオンしているユーザーだけです。

```
Get-WmiObject -Class Win32_ComputerSystem -Property UserName -ComputerName .
```

### コンピューターのローカル時刻を取得する
特定のコンピューターにおける現在のローカル時刻を取得するには、WMI Win32_LocalTime クラスを使用します。 このクラスでは、すべてのメタデータが既定で表示されるため、必要に応じて、**Select-Object** を使ってフィルター処理します。

```
PS> Get-WmiObject -Class Win32_LocalTime -ComputerName . | Select-Object -Property [a-z]*

Day          : 15
DayOfWeek    : 4
Hour         : 12
Milliseconds :
Minute       : 11
Month        : 6
Quarter      : 2
Second       : 52
WeekInMonth  : 3
Year         : 2006
```

### サービスの状態を表示する
前述のように、特定のコンピューターを対象に、すべてのサービスの状態を表示するには、ローカルで **Get-Service** コマンドレットを使用します。 リモート システムの場合は、WMI Win32_Service クラスを使用します。 **Select-Object** を併用して結果をフィルター処理し、**Status**、**Name**、**DisplayName** だけを抽出した場合は、**Get-Service** を実行した場合とほぼ同じ出力形式になります。

```
Get-WmiObject -Class Win32_Service -ComputerName . | Select-Object -Property Status,Name,DisplayName
```

非常に長い名前のサービスも存在します。こうしたサービスの名前全体を表示するには、**Format-Table** に **AutoSize** パラメーターと **Wrap** パラメーターを使用します。列幅が調整され、長い名前は切り詰められるのではなく折り返されます。

```
Get-WmiObject -Class Win32_Service -ComputerName . | Format-Table -Property Status,Name,DisplayName -AutoSize -Wrap
```




<!--HONumber=Oct16_HO1-->


