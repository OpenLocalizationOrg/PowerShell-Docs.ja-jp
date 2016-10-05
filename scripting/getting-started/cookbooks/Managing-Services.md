---
title: "サービスの管理"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 7a410e4d-514b-4813-ba0c-0d8cef88df31
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 66c2a8c8afab49f16e8ef7d0b5ba3a2a65c92490

---

# サービスの管理
サービスに関連したさまざまなタスクを実行するために設計された 8 つの主要な Service コマンドレットがあります。 一覧に表示され、実行の各サービスの状態を変更するだけを使用してサービス コマンドレットの一覧を取得できます **Get-help \ (& a) #42;-サービス**, とを使用して、各サービスのコマンドレットに関する情報を見つけられる **Get-help < コマンドレット名 >**, など **Get-help 新規サービス**します。

## サービスの取得
**Get-Service** コマンドレットを使用して、ローカル コンピューターまたはリモート コンピューター上のサービスを取得できます。 **Get-Process** と同様、パラメーターを指定せずに **Get-Service** コマンドを実行した場合、すべてのサービスが返されます。 アスタリスクをワイルドカードとして使用し、サービスを名前でフィルター処理するには、次のように入力します。

```
PS> Get-Service -Name se*
Status   Name               DisplayName
------   ----               -----------
Running  seclogon           Secondary Logon
Running  SENS               System Event Notification
Stopped  ServiceLayer       ServiceLayer
```

サービスの実際の名前がわかるとは限らないため、表示名でサービスを検索しなければならない場合もあります。 特定の名前を指定するか、ワイルドカードを使用するか、または表示名の一覧を使用することによって検索できます。

```
PS> Get-Service -DisplayName se*
Status   Name               DisplayName
------   ----               -----------
Running  lanmanserver       Server
Running  SamSs              Security Accounts Manager
Running  seclogon           Secondary Logon
Stopped  ServiceLayer       ServiceLayer
Running  wscsvc             Security Center
PS> Get-Service -DisplayName ServiceLayer,Server
Status   Name               DisplayName
------   ----               -----------
Running  lanmanserver       Server
Stopped  ServiceLayer       ServiceLayer
```

Get-Service コマンドレットの ComputerName パラメーターを使用して、リモート コンピューター上のサービスを取得できます。 ComputerName パラメーターには複数の値およびワイルドカード文字を使用できるため、1 つのコマンドで複数のコンピューター上のサービスを取得できます。 たとえば、次のコマンドでは、Server01 リモート コンピューター上のサービスを取得できます。

```
Get-Service -ComputerName Server01
```

## 必要なサービスおよび依存するサービスの取得
Get-Service コマンドレットには、サービスの管理に非常に便利な 2 つのパラメーターがあります。 DependentServices パラメーターは、そのサービスに依存するサービスを取得します。 RequiredServices パラメーターは、そのサービスが依存しているサービスを取得します。

これらのパラメーターは、Get-Service が返す System.ServiceProcess.ServiceController オブジェクトの DependentServices プロパティと ServicesDependedOn (エイリアス: RequiredServices) プロパティの値を表示するだけですが、コマンドが単純化され、この情報をずっと簡単に取得できるようになります。

次のコマンドは、LanmanWorkstation サービスが必要とするサービスを取得します。

```
PS> Get-Service -Name LanmanWorkstation -RequiredServices
Status   Name               DisplayName
------   ----               -----------
Running  MRxSmb20           SMB 2.0 MiniRedirector
Running  bowser             Bowser
Running  MRxSmb10           SMB 1.x MiniRedirector
Running  NSI                Network Store Interface Service
```

次のコマンドは、LanmanWorkstation サービスを必要とするサービスを取得します。

```
PS> Get-Service -Name LanmanWorkstation -DependentServices
Status   Name               DisplayName
------   ----               -----------
Running  SessionEnv         Terminal Services Configuration
Running  Netlogon           Netlogon
Stopped  Browser            Computer Browser
Running  BITS               Background Intelligent Transfer Ser...
```

また、依存関係を持つサービスをすべて取得することもできます。 次のコマンドは、それを実行した後、Format-Table コマンドレットを使用して、コンピューター上のサービスの Status、Name、RequiredServices、DependentServices の各プロパティを表示します。

```
Get-Service -Name * | where {$_.RequiredServices -or $_.DependentServices} | Format-Table -Property Status, Name, RequiredServices, DependentServices -auto
```

## サービスの停止、開始、一時停止、再開
サービスを扱うすべてのコマンドレットは、同じ形式で指定できます。 サービスを一般的な名前 (表示名) で指定できるほか、表示名の一覧を指定したり、ワイルドカードを値として使用したりすることもできます。 印刷スプーラーを停止するには、次のように入力します。

```
Stop-Service -Name spooler
```

停止した印刷スプーラーを開始するには、次のように入力します。

```
Start-Service -Name spooler
```

印刷スプーラーを一時停止するには、次のように入力します。

```
Suspend-Service -Name spooler
```

**Restart-Service** コマンドレットの動作も、サービスを扱う他のコマンドレットと同じですが、もう少し複雑な例を示します。 最も簡単な使い方は、サービスの名前を指定することです。

```
PS> Restart-Service -Name spooler
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
PS>
```

印刷スプーラーの起動に関して、警告メッセージが繰り返し出力されていることがわかります。 ある程度時間のかかるサービス操作を実行すると、タスクの実行を試みている最中であることを示すメッセージが表示されます。

複数のサービスを再開する場合は、サービスの一覧を取得してからフィルターを適用し、その上でサービスを再開するようにします。

```
PS> Get-Service | Where-Object -FilterScript {$_.CanStop} | Restart-Service
WARNING: Waiting for service 'Computer Browser (Browser)' to finish stopping...
WARNING: Waiting for service 'Computer Browser (Browser)' to finish stopping...
Restart-Service : Cannot stop service 'Logical Disk Manager (dmserver)' because
 it has dependent services. It can only be stopped if the Force flag is set.
At line:1 char:57
+ Get-Service | Where-Object -FilterScript {$_.CanStop} | Restart-Service <<<<
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
WARNING: Waiting for service 'Print Spooler (Spooler)' to finish starting...
```

サービスを扱うこれらのコマンドレットには ComputerName パラメーターはありませんが、Invoke-Command コマンドレットを使用すれば、これらのコマンドレットをリモート コンピューター上で実行できます。 たとえば、次のコマンドでは、Server01 リモート コンピューター上のスプーラー サービスを再起動できます。

```
Invoke-Command -ComputerName Server01 {Restart-Service Spooler}
```

## サービスのプロパティの設定
Set-Service コマンドレットは、ローカル コンピューターまたはリモート コンピューター上のサービスのプロパティを変更します。 サービスの状態もプロパティの 1 つであるため、このコマンドレットを使用してサービスを開始、停止、一時停止できます。 Set-Service コマンドレットには、サービスのスタートアップの種類を変更できる StartupType パラメーターもあります。

Windows Vista 以降のバージョンの Windows で、Set-Service を使用するには、Windows PowerShell を開く際に [管理者として実行] オプションを指定する必要があります。

詳細については、「[Set-Service [m2]](https://technet.microsoft.com/en-us/library/b71e29ed-372b-4e32-a4b7-5eb6216e56c3)」を参照してください。

## 参照
[Get-Service [m2]](https://technet.microsoft.com/en-us/library/0a09cb22-0a1c-4a79-9851-4e53075f9cf6)
[Set-Service [m2]](https://technet.microsoft.com/en-us/library/b71e29ed-372b-4e32-a4b7-5eb6216e56c3)
[Restart-Service [m2]](https://technet.microsoft.com/en-us/library/45acf50d-2277-4523-baf7-ce7ced977d0f)
[Suspend-Service [m2]](https://technet.microsoft.com/en-us/library/c8492b87-0e21-4faf-8054-3c83c2ec2826)




<!--HONumber=Oct16_HO1-->


