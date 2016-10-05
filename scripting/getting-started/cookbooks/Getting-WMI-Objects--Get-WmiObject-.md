---
title: "WMI オブジェクトの取得 (Get-WmiObject)"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: f0ddfc7d-6b5e-4832-82de-2283597ea70d
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: de9e8af7908ac6a72a100fd2c86b52c30c390c39

---

# WMI オブジェクトの取得 (Get-WmiObject)

## WMI オブジェクトの取得 (Get-WmiObject)
Windows Management Instrumentation (WMI) は、Windows システム管理のための中核となるテクノロジであり、幅広い種類の情報を一貫した方法で公開します。 WMI によって可能になるタスクが非常に多いことから、WMI オブジェクトにアクセスするための Windows PowerShell コマンドレットである **Get-WmiObject** は、実際の作業を行うための最も便利なコマンドレットの 1 つと言えます。 ここでは、Get-WmiObject を使って WMI オブジェクトにアクセスする方法と、WMI オブジェクトを使って特定の作業を行う方法について説明します。

### WMI クラスの一覧を取得する
WMI のほとんどのユーザーが直面する最初の問題は、WMI で何ができるかを調べることです。 WMI クラスは、管理できるリソースを記述しています。 何百もの WMI クラスがあり、その中には数十個のプロパティを持つクラスもあります。

この問題に対処するため、**Get-WmiObject** では WMI を探索可能にしました。 ローカル コンピューター上で使える WMI クラスの一覧を取得するには、次のように入力します。

```
PS> Get-WmiObject -List

__SecurityRelatedClass                  __NTLMUser9X
__PARAMETERS                            __SystemSecurity
__NotifyStatus                          __ExtendedStatus
Win32_PrivilegesStatus                  Win32_TSNetworkAdapterSettingError
Win32_TSRemoteControlSettingError       Win32_TSEnvironmentSettingError
...
```

同じ情報をリモート コンピューターから取得するには、次のように、ComputerName パラメーターにコンピューター名や IP アドレスを指定します。

```
PS> Get-WmiObject -List -ComputerName 192.168.1.29

__SystemClass                           __NAMESPACE
__Provider                              __Win32Provider
__ProviderRegistration                  __ObjectProviderRegistration
...
```

リモート コンピューターから返されるクラスの一覧は、そのコンピューターで実行されている特定のオペレーティング システムや、インストールされているアプリケーションによって追加された特定の WMI 拡張機能に応じて異なることがあります。

> [!NOTE]
> Get-WmiObject を使ってリモート コンピューターに接続するときは、リモート コンピューターで WMI が実行されている必要があり、既定の構成の場合、接続に使うアカウントがリモート コンピューターのローカル管理者グループに属している必要があります。 リモート システムに Windows PowerShell をインストールする必要はありません。 そのため、WMI が利用可能であれば、Windows PowerShell を実行していないオペレーティング システムであっても管理できます。

また、ローカル システムに接続するときに、コンピューター名を含めることもできます。 ローカル コンピューターの名前、IP アドレス (またはループバック アドレス 127.0.0.1)、WMI スタイル '.' をコンピューター名として使うことができます。 IP アドレスが 192.168.1.90 で、Admin01 という名前のコンピューターで Windows PowerShell を実行している場合、次のコマンドはそのコンピューターのすべての WMI クラスの一覧を返します。

```
Get-WmiObject -List
Get-WmiObject -List -ComputerName .
Get-WmiObject -List -ComputerName Admin01
Get-WmiObject -List -ComputerName 192.168.1.90
Get-WmiObject -List -ComputerName 127.0.0.1
Get-WmiObject -List -ComputerName localhost
```

Get-WmiObject は、既定では root/cimv2 名前空間を使います。 別の WMI 名前空間を指定する場合は、**Namespace** パラメーターを使って、対応する名前空間のパスを指定します。

```
PS> Get-WmiObject -List -ComputerName 192.168.1.29 -Namespace root

__SystemClass                           __NAMESPACE
__Provider                              __Win32Provider
...
```

### WMI クラスの詳細を表示する
WMI クラスの名前がわかっている場合は、その名前を使って情報をすぐに取得できます。 たとえば、コンピューターに関する情報を取得するためによく使われる WMI クラスの 1 つに、**Win32_OperatingSystem** があります。

```
PS> Get-WmiObject -Class Win32_OperatingSystem -Namespace root/cimv2 -ComputerName .

SystemDirectory : C:\WINDOWS\system32
Organization    : Global Network Solutions
BuildNumber     : 2600
RegisteredUser  : Oliver W. Jones
SerialNumber    : 12345-678-9012345-67890
Version         : 5.1.2600
```

ここでは、すべてのパラメーターを示しましたが、このコマンドはもっと簡潔に表現できます。 **ComputerName** パラメーターは、ローカル システムに接続するときには必要ありません。 最も一般的なケースを示し、このパラメーターを思い出してもらうために使いました。 **Namespace** の既定値は root/cimv2 であるため、これも省略できます。 最後に、ほとんどのコマンドレットでは、共通のパラメーターの名前を省略できます。 Get-WmiObject の場合、最初のパラメーターに名前が指定されていないときには、Windows PowerShell はそれを **Class** パラメーターとして扱います。 したがって、先ほどのコマンドは、次のように入力することもできました。

```
Get-WmiObject Win32_OperatingSystem
```

**Win32_OperatingSystem** クラスには、ここで紹介した以外にも多数のプロパティがあります。 Get-Member を使うと、すべてのプロパティを参照できます。 WMI クラスのプロパティは、他のオブジェクト プロパティと同じように自動的に取得できます。

```
PS> Get-WmiObject -Class Win32_OperatingSystem -Namespace root/cimv2 -ComputerName . | Get-Member -MemberType Property

   TypeName: System.Management.ManagementObject#root\cimv2\Win32_OperatingSyste
m

Name                                      MemberType Definition
----                                      ---------- ----------
__CLASS                                   Property   System.String __CLASS {...
...
BootDevice                                Property   System.String BootDevic...
BuildNumber                               Property   System.String BuildNumb...
...
```

#### 既定以外のプロパティを Format コマンドレットで表示する
**Win32_OperatingSystem** クラスに含まれている情報のうち、既定では表示されない情報を表示するには、**Format** コマンドレットを使います。 たとえば、利用可能なメモリのデータを表示するには、次のように入力します。

```
PS> Get-WmiObject -Class Win32_OperatingSystem -Namespace root/cimv2 -ComputerName . | Format-Table -Property TotalVirtualMemorySize,TotalVisibleMemorySize,FreePhysicalMemory,FreeVirtualMemory,FreeSpaceInPagingFiles

TotalVirtualMemorySize TotalVisibleMem FreePhysicalMem FreeVirtualMemo FreeSpaceInPagi
                              ory              ry         ngFiles
--------------- --------------- --------------- --------------- ---------------
        2097024          785904          305808         2056724         1558232
```

> [!NOTE]
> ワイルドカードのプロパティ名を使用 **Format-table**, パイプラインの最後の要素に変形できるため、 **Format-table-プロパティ TotalV\ &#42";"、"無料"&"#42 です。**

メモリのデータは、次のように入力して一覧の形式にすると、さらに読みやすくなります。

```
PS> Get-WmiObject -Class Win32_OperatingSystem -Namespace root/cimv2 -ComputerName . | Format-List TotalVirtualMemorySize,TotalVisibleMemorySize,FreePhysicalMemory,FreeVirtualMemory,FreeSpaceInPagingFiles

TotalVirtualMemorySize : 2097024
TotalVisibleMemorySize : 785904
FreePhysicalMemory     : 301876
FreeVirtualMemory      : 2056724
FreeSpaceInPagingFiles : 1556644
```




<!--HONumber=Oct16_HO1-->


