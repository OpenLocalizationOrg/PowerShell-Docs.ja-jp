---
title: "コンピューターの状態を変更する"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 8093268b-27f8-4a49-8871-142c5cc33f01
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 1779b9de13a30a43236e24793e5196261a7db77f

---

# コンピューターの状態を変更する
Windows PowerShell でコンピューターをリセットするには、標準のコマンド ライン ツールまたは WMI クラスを使用します。 Windows PowerShell は単にツールを実行するために使用するだけですが、Windows PowerShell でコンピューターの電力状態を変更する手順には、Windows PowerShell での外部ツールの使用方法に関する重要な詳細も含まれています。

### コンピューターをロックする
一般的に入手可能なツールを使ってコンピューターを直接ロックする唯一の方法は、**user32.dll** の **LockWorkstation()** 関数を呼び出すことです。

```
rundll32.exe user32.dll,LockWorkStation
```

このコマンドを実行すると、ワークステーションが直ちにロックされます。 このコマンドでは、*rundll32.exe* を使用して Windows DLL を実行 (および、繰り返し使用できるようにそれらのライブラリを保存) することにより、Windows の管理関数のライブラリである user32.dll を実行します。

Windows XP の場合など、ユーザーの簡易切り替えが有効なときにワークステーションをロックすると、コンピューターでは、現在のユーザーのスクリーン セーバーが起動するのではなく、ユーザーのログオン画面が表示されます。

ターミナル サーバーで特定のセッションをシャットダウンするには、**tsshutdn.exe** コマンド ライン ツールを使用します。

### 現在のセッションからログオフする
ローカル システム上でセッションからログオフする場合、いくつかの方法が考えられます。 最も簡単な方法は、リモート デスクトップ/ターミナル サービスのコマンドライン ツールである **logoff.exe** を使用することです (詳細については、Windows PowerShell プロンプトで **logoff /?** と入力してください)。 現在アクティブなセッションからログオフするには、引数を付けずに **logoff** と入力します。

また、**shutdown.exe** ツールにログオフのオプションを指定することもできます。

```
shutdown.exe -l
```

3 つ目は、WMI を使用する方法です。 Win32_OperatingSystem クラスには、Win32Shutdown メソッドがあります。 このメソッドに 0 フラグを指定して呼び出すと、ログオフが開始されます。

```
(Get-WmiObject -Class Win32_OperatingSystem -ComputerName .).Win32Shutdown(0)
```

詳細について、および Win32Shutdown メソッドの他の機能については、MSDN の「Win32_OperatingSystem クラスの Win32Shutdown メソッド」をご覧ください。

### コンピューターをシャットダウンまたは再起動する
通常、コンピューターのシャットダウンと再起動は同じ種類に属するタスクです。 コンピューターをシャットダウンできるツールであれば、コンピューターを再起動することもできます。逆にコンピューターを再起動できるツールであれば、コンピューターをシャットダウンすることもできます。 Windows PowerShell からコンピューターを簡単に再起動する方法としては、2 とおりの方法があります。 Tsshutdn.exe または Shutdown.exe に適切な引数を指定して実行することです。 詳しい使用方法は、**tsshutdn.exe ?** または **shutdown.exe ?** を実行すると参照できます。

シャットダウン操作と再起動操作は、Windows PowerShell から直接 **Win32_OperatingSystem** を使用して実行することもできます。

コンピューターをシャットダウンするには、**1** フラグを指定して Win32Shutdown メソッドを使用します。

```
(Get-WmiObject -Class Win32_OperatingSystem -ComputerName .).Win32Shutdown(1)
```

オペレーティング システムを再起動するには、**2** フラグを指定して Win32Shutdown メソッドを使用します。

```
(Get-WmiObject -Class Win32_OperatingSystem -ComputerName .).Win32Shutdown(2)
```




<!--HONumber=Oct16_HO1-->


