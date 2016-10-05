---
title: "複数のオブジェクトのタスクを繰り返す (ForEach-Object)"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 6697a12d-2470-4ed6-b5bb-c35e5d525eb6
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 8dd7904611fb697f9b398161e12765454b4f3441

---

# 複数のオブジェクトのタスクを繰り返す (ForEach-Object)
**ForEach-Object** コマンドレットは、現在のパイプライン オブジェクトのスクリプト ブロックと $_ 記述子を使用して、パイプライン内の各オブジェクトのコマンドを実行できるようにします。 これは、いくつかの複雑なタスクを実行するために使用できます。

これが有用な状況の一つは、データをさらに有効活用するよう操作する場合です。 たとえば、WMI からの Win32_LogicalDisk クラスは、各ローカル ディスクの空き領域の情報を返すのに使用できます。 データはバイト単位で返されますが、次のように、読み取るのは困難です。

```
PS> Get-WmiObject -Class Win32_LogicalDisk

DeviceID     : C:
DriveType    : 3
ProviderName :
FreeSpace    : 50665070592
Size         : 203912880128
VolumeName   : Local Disk
```

FreeSpace の値を 1024 で 2 回割ると、メガバイト単位に変換できます。最初の除算の後、データはキロバイト単位になり、2 回目の除算の後、メガバイト単位になります。 ForEach-Object のスクリプト ブロックでこのことを実行できます。次のように入力します。

```
Get-WmiObject -Class Win32_LogicalDisk | ForEach-Object -Process {($_.FreeSpace)/1024.0/1024.0}
48318.01171875
```

残念ながら、出力はラベルとは関連付けられていないデータになります。 この例のように、WMI プロパティは読み取り専用なので、FreeSpace を直接変換することはできません。 次のように入力するとします。

```
Get-WmiObject -Class Win32_LogicalDisk | ForEach-Object -Process {$_.FreeSpace = ($_.FreeSpace)/1024.0/1024.0}
```

次のようなエラー メッセージが表示されます。

```
"FreeSpace" is a ReadOnly property.
At line:1 char:70
+ Get-WmiObject -Class Win32_LogicalDisk | ForEach-Object -Process {$_.F <<<< r
eeSpace = ($_.FreeSpace)/1024.0/1024.0}
```

高度な技法を幾つか使用してデータを再編成できますが、よりシンプルな方法は、**Select-Object** を使用して新しいオブジェクトを作成することです。




<!--HONumber=Oct16_HO1-->


