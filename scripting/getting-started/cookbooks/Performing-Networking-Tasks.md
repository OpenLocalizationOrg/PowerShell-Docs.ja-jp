---
title: "ネットワーク関連タスクの実行"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a43cc55f-70c1-45c8-9467-eaad0d57e3b5
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 39266e1e4ae2101de26277c20a98596f62cf223d

---

# ネットワーク関連タスクの実行
TCP/IP は最も一般的に使用されるネットワーク プロトコルです。そのため、TCP/IP に関連したタスクは、最も低レベルのネットワーク プロトコル管理タスクと言えます。 このセクションでは、Windows PowerShell および WMI を使用して、これらのタスクを実行します。

### コンピューターの IP アドレスの一覧表示
ローカル コンピューターで使用されているすべての IP アドレスを取得するには、次のコマンドを使用します。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Format-Table -Property IPAddress
```

このコマンドの出力結果を見ると、値が中かっこで囲まれており、他の多くのプロパティの一覧表示結果とは異なることがわかります。

<pre>IPAddress
---------
{192.168.1.80} {192.168.148.1} {192.168.171.1} {0.0.0.0}</pre>

中かっこで囲まれている理由を理解するには、Get-Member コマンドレットを使用して **IPAddress** プロパティを調べます。

<pre>PS> Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Get-Member -Name IPAddress TypeName: System.Management.ManagementObject#root\cimv2\Win32_NetworkAdapter Configuration Name      MemberType Definition ----      ---------- ---------- IPAddress Property   System.String[] IPAddress {get;}</pre>

各ネットワーク アダプターの IPAddress プロパティは、実際には配列です。 Definition 列に示されている中かっこは、**IPAddress** が **System.String** 値ではなく、**System.String** 値の配列であることを示しています。

### IP 構成データの一覧表示
各ネットワーク アダプターの詳しい IP 構成データを表示するには、次のコマンドを使用します。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName .
```

ネットワーク アダプター構成オブジェクトに対して既定で表示される情報は、確認できる情報のごく一部のみです。 詳細な調査とトラブルシューティングを行う場合は、Select-Object または書式設定用コマンドレット (Format-List など) を使用して、表示するプロパティを指定します。

最新の TCP/IP ネットワークなどで、IPX や WINS のプロパティが不要な場合は、Select-Object の ExcludeProperty パラメーターを使用して、名前が "WINS" または "IPX" で始まるすべてのプロパティを非表示にすることもできます。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=TRUE -ComputerName . | Select-Object -Property [a-z]* -ExcludeProperty IPX*,WINS*
```

このコマンドは、DHCP、DNS、ルーティングなど、IP に関連した従属的な構成プロパティについて詳しい情報を返します。

### コンピューターへの ping の送信
**Win32_PingStatus** を使用すると、コンピューターに対して簡単に ping を送信できます。 次のコマンドは ping を実行しますが、長い出力が返されます。

```
Get-WmiObject -Class Win32_PingStatus -Filter "Address='127.0.0.1'" -ComputerName .
```

次のコマンドでは、概要を把握しやすくするため、Address、ResponseTime、StatusCode の各プロパティだけを表示しています。 Format-Table の Autosize パラメーターを使用すると、Windows PowerShell で適切に表示されるように表の列のサイズが変更されます。

```
PS> Get-WmiObject -Class Win32_PingStatus -Filter "Address='127.0.0.1'" -ComputerName . | Format-Table -Property Address,ResponseTime,StatusCode -Autosize

Address   ResponseTime StatusCode
-------   ------------ ----------
127.0.0.1            0          0
A status code of 0 indicates a successful ping.
```

配列を使用すると、1 つのコマンドで複数のコンピューターに ping を送信できます。 複数のアドレスが存在するため、**ForEach-Object** を使用して、各アドレスに対して個別に ping を送信します。

```
"127.0.0.1","localhost","research.microsoft.com" | ForEach-Object -Process {Get-WmiObject -Class Win32_PingStatus -Filter ("Address='" + $_ + "'") -ComputerName .} | Select-Object -Property Address,ResponseTime,StatusCode
```

同じコマンド形式を使用して、サブネット上のすべてのコンピューターに ping を送信できます。たとえば、ネットワーク番号が 192.168.1.0 で、標準的なクラス C のサブネット マスク (255.255.255.0) を使用しているプライベート ネットワークを調べる場合、実際に調べる必要があるローカル アドレスは 192.168.1.1 ～ 192.168.1.254 の範囲のアドレスだけです (0 および 255 は、それぞれネットワーク番号およびサブネットのブロードキャスト アドレスとして常に予約されています)。

Windows PowerShell で 1 ～ 254 の数値の配列を表現するには、**1..254** というステートメントを使用します。 まず、この配列を生成した後、それぞれの値を、ping ステートメントのアドレスの一部として使用すると、サブネット全体に対して ping を送信できます。

```
1..254| ForEach-Object -Process {Get-WmiObject -Class Win32_PingStatus -Filter ("Address='192.168.1." + $_ + "'") -ComputerName .} | Select-Object -Property Address,ResponseTime,StatusCode
```

このテクニックは、一定範囲のアドレスを生成する場合だけでなく、さまざまな状況で応用できます。 アドレスの完全なセットを生成するには、次のように入力します。

`$ips = 1..254 | ForEach-Object -Process {"192.168.1." + $_}`

### ネットワーク アダプターのプロパティの取得
このユーザーズ ガイドの前半で、**Win32_NetworkAdapterConfiguration** を使用して一般的な構成プロパティを取得できることを説明しました。 厳密には TCP/IP 情報とは言えませんが、MAC アドレスやアダプター タイプなどのネットワーク アダプター情報は、コンピューターでどのようなことが起こっているかを把握する上で有益な手段です。 この情報の要約を取得するには、次のコマンドを使用します。

```
Get-WmiObject -Class Win32_NetworkAdapter -ComputerName .
```

### ネットワーク アダプターの DNS ドメインの割り当て
自動名前解決のために DNS ドメインを割り当てるには、**Win32_NetworkAdapterConfiguration SetDNSDomain** メソッドを使用します。 DNS ドメインは、各ネットワーク アダプター構成に対して別々に割り当てることになります。したがって、**ForEach-Object** ステートメントを使用して、各アダプターにドメインを割り当てる必要があります。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=true -ComputerName . | ForEach-Object -Process { $_. SetDNSDomain("fabrikam.com") }
```

ここでは、フィルター処理ステートメントとして、"IPEnabled=true" が必要です。その理由は、TCP/IP だけを使用したネットワークでも、コンピューター上のネットワーク アダプター構成のいくつかが、実際には TCP/IP アダプターではないためです。これらは、すべてのアダプターについて RAS、PPTP、QoS などのサービスをサポートする汎用的なソフトウェア要素であるため、自己のアドレスを持ちません。

**Get-WmiObject** フィルターを使う代わりに、**Where-Object** コマンドレットを使用して、コマンドをフィルター処理することもできます。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName . | Where-Object -FilterScript {$_.IPEnabled} | ForEach-Object -Process {$_.SetDNSDomain("fabrikam.com")}
```

### DHCP の構成タスクの実行
DNS の構成と同様、DHCP 情報の変更には、一連のネットワーク アダプターに対する操作が伴います。 WMI で実行できる操作はさまざまですが、ここでは、その中でも一般的なものをいくつか選んで説明します。

#### DHCP 対応アダプターの特定
コンピューター上の DHCP 対応アダプターを検索するには、次のコマンドを使用します。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=true" -ComputerName .
```

IP 構成の問題があるアダプターを除外するために、IP 対応アダプターだけを検索できます。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName .
```

#### DHCP のプロパティの取得
通常、アダプターの DHCP 関連のプロパティは先頭に "DHCP" が付くため、Format-Table の Property パラメーターを使用することで、それらのプロパティだけを表示できます。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "DHCPEnabled=true" -ComputerName . | Format-Table -Property DHCP*
```

#### 各アダプターの DHCP の有効化
すべてのアダプターで DHCP を有効にするには、次のコマンドを使用します。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter IPEnabled=true -ComputerName . | ForEach-Object -Process {$_.EnableDHCP()}
```

既に有効化されている DHCP を除外するために、**Filter** ステートメントとして "IPEnabled=true and DHCPEnabled=false" を使用できます。ただし、この手順を省略しても、エラーは発生しません。

#### 特定のアダプターの DHCP リースの解放および更新
**Win32_NetworkAdapterConfiguration** クラスには **ReleaseDHCPLease** メソッドと **RenewDHCPLease** メソッドがあります。 使用方法はどちらも同じです。 通常、これらのメソッドを使用するのは、特定のサブネット上に存在するアダプターのアドレスを解放または更新する必要がある場合だけです。 サブネット上のアダプターをフィルター処理する最も簡単な方法は、対応するサブネットのゲートウェイを使用したアダプター構成だけを選ぶことです。 たとえば、次のコマンドでは、DHCP リースを 192.168.1.254 から取得しているローカル コンピューターについて、アダプターの DHCP リースがすべて解放されます。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName . | Where-Object -FilterScript {$_.DHCPServer -contains "192.168.1.254"} | ForEach-Object -Process {$_.ReleaseDHCPLease()}
```

DHCP リースを更新する場合は、**ReleaseDHCPLease** メソッドの代わりに **RenewDHCPLease** メソッドを使用するだけです。

```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -Filter "IPEnabled=true and DHCPEnabled=true" -ComputerName . | Where-Object -FilterScript {$_.DHCPServer -contains "192.168.1.254"} | ForEach-Object -Process {$_.ReleaseDHCPLease()}
```

> [!NOTE]
> これらのメソッドをリモート コンピューターに対して使用する場合は、接続に使用されているアダプターのリースが解放または更新されると、リモート システムへのアクセスが失われる可能性があることにご注意ください。

#### すべてのアダプターの DHCP リースの解放および更新
**Win32_NetworkAdapterConfiguration** の **ReleaseDHCPLeaseAll** メソッドと **RenewDHCPLeaseAll** メソッドを使用すれば、すべてのアダプターを対象に DHCP アドレスをグローバルに解放または更新できます。 ただし、リースの解放と更新をグローバルに実行する場合、実行の対象は、特定のアダプターではなく、WMI クラスになります。したがって、コマンドは特定のアダプターに適用するのではなく、WMI クラスに適用する必要があります。

すべての WMI クラスを一覧表示して、目的のクラスだけを名前で選べば、特定の WMI クラスの参照 (クラスのインスタンスではない) を取得できます。 たとえば、Win32_NetworkAdapterConfiguration クラスを取得するには、次のコマンドを実行します。

```
Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"}
```

コマンド全体をクラスとして扱い、その **ReleaseDHCPAdapterLease** メソッドを呼び出します。 次のコマンドでは、パイプライン要素 **Get-WmiObject** および **Where-Object** を丸かっこで囲むことにより、それらが最初に評価されるようになります。

```
( Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"} ).ReleaseDHCPLeaseAll()
```

**RenewDHCPLeaseAll** メソッドも、同じコマンド形式で呼び出すことができます。

```
( Get-WmiObject -List | Where-Object -FilterScript {$_.Name -eq "Win32_NetworkAdapterConfiguration"} ).RenewDHCPLeaseAll()
```

### ネットワーク共有の作成
ネットワーク共有を作成するには、**Win32_Share Create** メソッドを使用します。

```
(Get-WmiObject -List -ComputerName . | Where-Object -FilterScript {$_.Name -eq "Win32_Share"}).Create("C:\temp","TempShare",0,25,"test share of the temp folder")
```

Windows PowerShell で **net share** を使用して、共有を作成することもできます。

```
net share tempshare=c:\temp /users:25 /remark:"test share of the temp folder"
```

### ネットワーク共有の削除
ネットワーク共有を削除する場合も **Win32_Share** を使用できます。ただし、そのプロセスは共有を作成する場合と若干異なります。共有を作成する場合は、フィルターで **Win32_Share** クラスを取得していました。これに対し、共有を削除する場合は、削除対象となる特定の共有を取得する必要があります。 次のステートメントでは、"TempShare" という共有を削除します。

```
(Get-WmiObject -Class Win32_Share -ComputerName . -Filter "Name='TempShare'").Delete()
```

**net share** でも同じことを行うことができます。

```
PS> net share tempshare /delete
tempshare was deleted successfully.
```

### Windows でアクセス可能なネットワーク ドライブの接続
**New-PSDrive** コマンドレットを使用すると、Windows PowerShell ドライブを作成できます。しかし、この方法で作成されたドライブは、Windows PowerShell でしかアクセスできません。 新しいネットワーク ドライブを作成するには、**WScript.Network** という COM オブジェクトを使用します。 次のコマンドは、共有 \\FPS01\users をローカルの B ドライブにマッピングします。

```
(New-Object -ComObject WScript.Network).MapNetworkDrive("B:", "\\FPS01\users")
```

**net use** コマンドでも同じことを行うことができます。

```
net use B: \\FPS01\users
```

**WScript.Network** または net use でマッピングされたドライブは、Windows PowerShell から直ちにアクセスできるようになります。




<!--HONumber=Oct16_HO1-->


