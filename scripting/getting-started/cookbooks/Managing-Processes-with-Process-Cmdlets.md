---
title: "Process コマンドレットによるプロセスの管理"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 5038f612-d149-4698-8bbb-999986959e31
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 5a635485387bb367f4e43982085f9d36765a95e5

---

# Process コマンドレットによるプロセスの管理
Windows PowerShell で Process コマンドレットを使用して、Windows PowerShell のローカル プロセスおよびリモート プロセスを管理できます。

## プロセスの取得 (Get-Process)
ローカル コンピューターで実行されているプロセスを取得するには、パラメーターを指定せずに **Get-Process** を実行します。

特定のプロセスを取得するには、プロセス名またはプロセス ID を指定します。 次のコマンドを実行すると、アイドル状態のプロセスを取得できます。

```
PS> Get-Process -id 0
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
      0       0        0         16     0               0 Idle
```

場合によっては、コマンドレットからデータが返されないこともあります。しかし、**Get-Process** で ProcessId を使用してプロセスを指定した場合は、通常、実行中の既知のプロセスを取得することが目的であるため、一致するプロセスが見つからないときはエラーが発生します。 指定された ID と一致するプロセスが存在しない場合、その ID が間違っているか、プロセスが既に終了している可能性があります。

```
PS> Get-Process -Id 99
Get-Process : No process with process ID 99 was found.
At line:1 char:12
+ Get-Process  <<<< -Id 99
```

Get-Process コマンドレットの Name パラメーターを使用すると、プロセスのサブセットをプロセス名で指定できます。 Name パラメーターでは、コンマ区切り一覧を使用して複数の名前を指定できます。また、ワイルドカードを使用できるため、名前のパターンを入力できます。

たとえば、次のコマンドでは、名前が "ex" で始まるプロセスを取得できます。

```
PS> Get-Process -Name ex*
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    234       7     5572      12484   134     2.98   1684 EXCEL
    555      15    34500      12384   134   105.25    728 explorer
```

.NET System.Diagnostics.Process クラスは、Windows PowerShell のプロセスの基礎となるクラスです。そのため、Windows PowerShell のプロセスには、System.Diagnostics.Process の表記規則の一部が使用されています。 これらの規則の 1 つとして、実行可能ファイルのプロセス名の最後に ".exe" は付きません。

**Get-Process** の Name パラメーターには、複数の値を指定することもできます。

```
PS> Get-Process -Name exp*,power* 
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    540      15    35172      48148   141    88.44    408 explorer
    605       9    30668      29800   155     7.11   3052 powershell
```

Get-Process の ComputerName パラメーターを使用すると、リモート コンピューター上のプロセスを取得できます。 たとえば、次のコマンドでは、ローカル コンピューター ("localhost" で表す) と 2 台のリモート コンピューターの PowerShell プロセスを取得できます。

```
PS> Get-Process -Name PowerShell -ComputerName localhost, Server01, Server02
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    258       8    29772      38636   130            3700 powershell
    398      24    75988      76800   572            5816 powershell
    605       9    30668      29800   155     7.11   3052 powershell
```

ここではコンピューターの名前は表示されませんが、Get-Process から返されたプロセス オブジェクトの MachineName プロパティに格納されています。 次のコマンドでは、Format-Table コマンドレットを使用して、プロセス オブジェクトのプロセス ID、ProcessName、および MachineName (ComputerName) の各プロパティを表示します。

```
PS> Get-Process -Name PowerShell -ComputerName localhost, Server01, Server01 | Format-Table -Property ID, ProcessName, MachineName
  Id ProcessName MachineName
  -- ----------- -----------
3700 powershell  Server01
3052 powershell  Server02
5816 powershell  localhost
```

より複雑なこのコマンドは、Get-Process の標準の表示に MachineName プロパティを追加します。 アクサン グラーブ文字 (`) (ASCII 96) は、Windows PowerShell の連結文字です。

```
get-process powershell -computername localhost, Server01, Server02 | format-table -property Handles, `
                    @{Label="NPM(K)";Expression={[int]($_.NPM/1024)}}, `
                    @{Label="PM(K)";Expression={[int]($_.PM/1024)}}, `
                    @{Label="WS(K)";Expression={[int]($_.WS/1024)}}, `
                    @{Label="VM(M)";Expression={[int]($_.VM/1MB)}}, `
                    @{Label="CPU(s)";Expression={if ($_.CPU -ne $()` 
                    {$_.CPU.ToString("N")}}}, `                                                                         
                    Id, ProcessName, MachineName -auto

Handles  NPM(K)  PM(K) WS(K) VM(M) CPU(s)  Id ProcessName  MachineName
-------  ------  ----- ----- ----- ------  -- -----------  -----------
    258       8  29772 38636   130         3700 powershell Server01
    398      24  75988 76800   572         5816 powershell localhost
    605       9  30668 29800   155 7.11    3052 powershell Server02
```

## プロセスの停止 (Stop-Process)
Windows PowerShell では、さまざまな方法でプロセスを一覧表示できますが、プロセスの停止についてはどうでしょうか。

**Stop-Process** コマンドレットでは、停止するプロセスを Name または Id で指定できます。 プロセスを停止できるかどうかは、ユーザーのアクセス許可によって異なります。 停止することのできないプロセスもあります。 たとえば、アイドル状態のプロセスを停止しようとすると、エラーが発生します。

```
PS> Stop-Process -Name Idle
Stop-Process : Process 'Idle (0)' cannot be stopped due to the following error:
 Access is denied
At line:1 char:13
+ Stop-Process  <<<< -Name Idle
```

**Confirm** パラメーターを使用して、確認メッセージを強制的に表示することもできます。 ワイルドカードを使ってプロセス名を指定する場合、停止する必要のないプロセスに誤って一致する可能性があるため、このパラメーターは特に便利です。

```
PS> Stop-Process -Name t*,e* -Confirm
Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Process" on Target "explorer (408)".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):n
Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Process" on Target "taskmgr (4072)".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):n
```

オブジェクトのフィルター処理コマンドレットを使用すれば、複雑なプロセス操作を行うこともできます。 Process オブジェクトには、応答がない場合に True を返す Responding プロパティがあります。したがって、次のコマンドを使用すると、応答のないアプリケーションをすべて停止できます。

```
Get-Process | Where-Object -FilterScript {$_.Responding -eq $false} | Stop-Process
```

他の状況でも、これと同じアプローチを使用できます。 たとえば、あるアプリケーションを起動すると、補助的なアプリケーションが自動的に実行され、通知領域に表示されるとします。 このとき、ターミナル サービスのセッションで補助的なアプリケーションが正しく起動しないことがわかりました。しかし、物理的なコンピューター コンソールで実行されているセッションでは起動したままにしておく必要があります。 物理的なコンピューター デスクトップに関連付けられたセッションには、常にセッション ID として 0 が割り当てられます。したがって、**Where-Object** およびプロセスの **SessionId** を使用することで、その他のセッションに属するプロセスのすべてのインスタンスを停止できます。

```
Get-Process -Name BadApp | Where-Object -FilterScript {$_.SessionId -neq 0} | Stop-Process
```

Stop-Process コマンドレットには、ComputerName パラメーターがありません。 そのため、リモート コンピューター上でプロセスを停止するコマンドを実行するには、Invoke-Command コマンドレットを使用します。 たとえば、リモート コンピューター Server01 上で PowerShell プロセスを停止するには、次のように入力します。

```
Invoke-Command -ComputerName Server01 {Stop-Process Powershell}
```

## 他のすべての Windows PowerShell セッションの停止
現在のセッションを除いて、実行中のすべての Windows PowerShell セッションを停止できると便利な場合があります。 特定のセッションで大量のリソースが消費されていたり、セッションがアクセスできない状態になっている場合 (リモートから実行されていたり、別のデスクトップ セッションで使用されている場合など)、そのプロセスを直接停止できない場合があります。 しかし、実行中のすべてのセッションを停止しようとすると、現在のセッションまで停止されてしまう可能性があります。

Windows PowerShell の各セッションには、Windows PowerShell プロセスの ID を保持する環境変数 PID が割り当てられます。 $PID と各セッションの ID を照合することによって、異なる ID を持つ Windows PowerShell セッションだけを強制終了できます。 これは、次のパイプライン コマンドで実現できます。また、**PassThru** パラメーターを使用しているため、強制終了されたセッションが一覧表示されます。

```
PS> Get-Process -Name powershell | Where-Object -FilterScript {$_.Id -ne $PID} | Stop-Process -
PassThru
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    334       9    23348      29136   143     1.03    388 powershell
    304       9    23152      29040   143     1.03    632 powershell
    302       9    20916      26804   143     1.03   1116 powershell
    335       9    25656      31412   143     1.09   3452 powershell
    303       9    23156      29044   143     1.05   3608 powershell
    287       9    21044      26928   143     1.02   3672 powershell
```

## プロセスの開始、デバッグ、待機
Windows PowerShell には、プロセスを開始 (または再開) するためのコマンドレット、プロセスのデバッグ用コマンドレット、コマンドを実行する前にプロセスが完了するまで待機するためのコマンドレットが用意されています。 これらのコマンドレットについては、各コマンドレットのヘルプ トピックをご覧ください。

## 参照
[Get-Process [m2]](https://technet.microsoft.com/en-us/library/27a05dbd-4b69-48a3-8d55-b295f6225f15)
[Stop-Process [m2]](https://technet.microsoft.com/en-us/library/12454238-9881-457a-bde4-fb6cd124deec)
[Start-Process](https://technet.microsoft.com/en-us/library/41a7e43c-9bb3-4dc2-8b0c-f6c32962e72c)
[Wait-Process](https://technet.microsoft.com/en-us/library/9222af7a-789d-4a09-aa90-09d7c256c799)
[Debug-Process](https://technet.microsoft.com/en-us/library/eea1dace-3913-4dbd-b659-5a94a610eee1)
[Invoke-Command](https://technet.microsoft.com/en-us/library/22fd98ba-1874-492e-95a5-c069467b8462)




<!--HONumber=Oct16_HO1-->


