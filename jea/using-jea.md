---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "JEA の使用"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 9db7a5a91d25d459313117da34af63016f03c241

---

# JEA の使用
このセクションの焦点は、*JEA の使用*というエンド ユーザー エクスペリエンスを理解することです。
「前提条件」セクションで、デモ用の JEA エンドポイントを作成しました。
このデモを使用して、JEA の動作を示します。
エンド ユーザー エクスペリエンスを可能にしている操作とファイルについては、このガイドの後半で説明します。

## 管理者以外のユーザーとして JEA を使用する
JEA の動作を示すには、管理者以外のユーザーとして PowerShell リモート処理を使用する必要があります。
新しい PowerShell ウィンドウで次のコマンドを実行します。   

```PowerShell
$NonAdminCred = Get-Credential
```

入力を求められたら、管理者以外のアカウントの資格情報を入力します。
「[ユーザーとグループの設定](creating-a-domain-controller.md#set-up-users-and-groups)」セクションに従っている場合、資格情報は次のようになります。
-   ユーザー名 = "OperatorUser"
-   パスワード = "pa$$w0rd"

次に、次のコマンドを実行して、指定した資格情報を使用するデモ エンドポイントに接続します。

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEA_Demo -Credential $NonAdminCred
```

これで、ローカル コンピューターで対話型リモート PowerShell セッションに入りました。
"Credential" パラメーターの使用によって、OperatorUser (または使用したユーザー名の) アカウント*として*接続されています。
プロンプトが `[localhost]: PS>` に変化していることは、リモート セッションを操作していることを示します。  

リモート コマンド プロンプトで次のコマンドを実行して、自分が使用できるコマンドを表示します。

```PowerShell
Get-Command
```

お察しのように、表示されるのは、通常の PowerShell ウィンドウで使用できるコマンド (多くの場合、数千のコマンドを含む可能性があります) の非常に限定されたサブセットです。
具体的には、JEA の 8 つの既定のコマンド (Clear-Host、Exit-PSSession、Get-Command、Get-FormatData、Get-Help、Measure-Object、Out-Default、Select-Object) と、メンテナンス ロール ファイルに明示的に含まれている 2 つのコマンドだけが表示されます。

次に、メンテナンス ロール機能ファイルに含まれるユーザー定義関数を呼び出して、このセッションが動作しているユーザー コンテキストを調べてみましょう。

```PowerShell
Get-UserInfo
```

この関数の出力は、"ConnectedUser" と "RunAsUser" を示します。
接続されたユーザー ("ConnectedUser") は、リモート セッションに接続されたアカウントです (例:自分のアカウント)。
接続されたユーザーは、管理者特権を持つ必要はありません。
"RunAs" アカウントは、特権操作を実際に実行するアカウントです。
アカウントをユーザーとして接続し、特権ユーザーとして実行することで、管理権限を与えることなく、ユーザーが特定の管理タスクを実行することを許可できます。

この動作の実例を示すために、次のコマンドを実行します。

```PowerShell
Restart-Service -Name Spooler -Verbose
```

通常、Restart-Service を実行するには、管理者特権が必要です。
ただし、JEA 仮想アカウントでは、権限のない資格情報を使用して、このコマンドを実行できます。

このように、JEA では、既に使用したコマンドを使用して作業を実行できます。
それでは、使用を*許可されていない*コマンドの場合はどうなるでしょうか。
JEA セッションで別のコマンド (`Restart-Computer` など) を実行してみてください。そのようなコマンドは JEA によって実行が禁止されることを確認してください。

```PowerShell
[localhost]: PS> Restart-Computer
The term 'Restart-Computer' is not recognized as the name of a cmdlet, function, script file, or
operable program. Check the spelling of the name, or if a path was included, verify that the path
is correct and try again.
    + CategoryInfo          : ObjectNotFound: (Restart-Computer:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

最後に、制約付きの JEA エンドポイントから離れるために、次のコマンドを実行します。

```PowerShell
Exit-PSSession
```

これで、リモート PowerShell セッションから切断されます。




<!--HONumber=Oct16_HO1-->


