---
title: "リモート コマンドの実行"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: d6938b56-7dc8-44ba-b4d4-cd7b169fd74d
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d36a862d27ed4bb8a4bed2ae58f9479ba1b29d21

---

# リモート コマンドの実行
1 つの Windows PowerShell コマンドを使用し、1 台から数百台のコンピューターでコマンドを実行できます。 Windows PowerShell は、WMI、RPC、WS-Management など、さまざまなテクノロジを使用してリモート コンピューティングをサポートします。

## 設定をしないリモート処理
多くの Windows PowerShell コマンドレットは、1 台以上のリモート コンピューターのデータの収集や設定の変更が可能な ComputerName パラメーターを持っています。 Windows PowerShell がサポートするすべての Windows オペレーティング システムのさまざまな通信テクノロジや多くの作業を、特別な構成なしで利用します。

これらのコマンドレットには、次のものがあります。

-   [コンピューターの再起動](https://technet.microsoft.com/en-us/library/dd315301.aspx)

-   [接続のテスト](https://technet.microsoft.com/en-us/library/dd315259.aspx)

-   [イベント ログのクリア](https://technet.microsoft.com/en-us/library/dd347552.aspx)

-   [Get-eventlog](https://technet.microsoft.com/en-us/library/dd315250.aspx)

-   [修正プログラムの取得](https://technet.microsoft.com/en-us/library/e1ef636f-5170-4675-b564-199d9ef6f101)

-   [Get-process](https://technet.microsoft.com/en-us/library/dd347630.aspx)

-   [Get-service](https://technet.microsoft.com/en-us/library/dd347591.aspx)

-   [-サービス アカウントを設定](https://technet.microsoft.com/en-us/library/dd315324.aspx)

-   [Get-winevent](https://technet.microsoft.com/en-us/library/dd315358.aspx)

-   [Get-wmiobject](https://technet.microsoft.com/en-us/library/dd315295.aspx)

通常、特別な構成なしでリモート処理をサポートするコマンドレットは、ComputerName パラメーターを指定し、Session パラメーター必要はありません。 セッションでこれらのコマンドレットを見つけるには、次のように入力します。

```
Get-Command | where { $_.parameters.keys -contains "ComputerName" -and $_.parameters.keys -notcontains "Session"}
```

## Windows PowerShell のリモート処理
Windows PowerShell のリモート処理では、WS-Management プロトコルを使用し、1 台以上のリモート コンピューターで Windows PowerShell コマンドを実行できます。 これにより、永続的な接続の確立、1:1 対話型のセッションの開始、および複数のコンピューターでのスクリプトの実行が可能になります。

Windows PowerShell のリモート処理を使用するには、リモート管理のためにリモート コンピューターを構成する必要があります。 手順を含む詳細については、「[about_Remote_Requirements](https://technet.microsoft.com/en-us/library/dd315349.aspx)」をご覧ください。

Windows PowerShell のリモート処理を構成した後は、多くのリモート処理の戦略が使用可能になります。 このドキュメントの残りの部分では、その一部だけを取り上げます。 詳細については、[リモート コマンドに関するページ](https://technet.microsoft.com/en-us/library/dd347744.aspx)と[リモート コマンドの FAQ に関するページ](https://technet.microsoft.com/en-us/library/dd347744.aspx)をご覧ください。

### 対話型セッションの開始
1 台のリモート コンピューターとの対話型セッションを開始するには、[Enter-PSSession](https://technet.microsoft.com/en-us/library/dd315384.aspx) コマンドレットを使用します。 たとえば、Server01 リモート コンピューターと対話型セッションを開始するには、次のように入力します。

```
Enter-PSSession Server01
```

コマンド プロンプトが、接続しているコンピューターの名前を表示するよう変更されます。 これ以降、プロンプトで入力したコマンドはリモート コンピューターで実行され、結果はローカル コンピューターに表示されます。

対話型セッションを終了するには、次のように入力します。

```
Exit-PSSession
```

Enter-PSSession コマンドレットと Exit-PSSession コマンドレットの詳細については、「[Enter-PSSession](https://technet.microsoft.com/en-us/library/dd315384.aspx)」と「[Exit-PSSession](https://technet.microsoft.com/en-us/library/dd315322.aspx)」を参照してください。

### リモート コマンドの実行
1 台以上のリモート コンピューターでいずれかのコマンドを実行するには、[Invoke-Command](https://technet.microsoft.com/en-us/library/dd347578.aspx) コマンドレットを使用します。
たとえば、[Get-UICulture](https://technet.microsoft.com/en-us/library/dd347742.aspx) コマンドをリモート コンピューター Server01 と Server02 で実行するには、次のように入力します。

```
Invoke-Command -ComputerName Server01, Server02 {Get-UICulture}
```

コンピューターに出力が返されます。

```
LCID    Name     DisplayName               PSComputerName
----    ----     -----------               --------------
1033    en-US    English (United States)   server01.corp.fabrikam.com
1033    en-US    English (United States)   server02.corp.fabrikam.com
```

Invoke-Command コマンドレットの詳細については、「[Invoke-Command](https://technet.microsoft.com/en-us/library/22fd98ba-1874-492e-95a5-c069467b8462)」を参照してください。

### スクリプトの実行
1 台以上のリモート コンピューターでスクリプトを実行するには、Invoke-Command コマンドレットの FilePath パラメーターを使用します。 スクリプトがローカル コンピューターでアクセス可能でなければなりません。 結果はローカル コンピューターに返されます。

たとえば、次のコマンドは、リモート コンピューター Server01 と Server02 で DiskCollect.ps1 スクリプトを実行します。

```
Invoke-Command -ComputerName Server01, Server02 -FilePath c:\Scripts\DiskCollect.ps1
```

Invoke-Command コマンドレットの詳細については、「[Invoke-Command](https://technet.microsoft.com/en-us/library/dd347578.aspx)」を参照してください。

### 永続的な接続の確立
データを共有する一連の関連コマンドを実行するには、リモート コンピューターでセッションを作成し、Invoke-Command コマンドレットを使用して作成したセッションでコマンドを実行します。 リモート セッションを作成するには、New-PSSession コマンドレットを使用します。

たとえば、次のコマンドは、コンピューター Server01 でリモート セッションを作成し、コンピューター Server02 で別のリモート セッションを作成します。 セッション オブジェクトは $s 変数に保存されます。

```
$s = New-PSSession -ComputerName Server01, Server02
```

セッションが確立されたので、それらで任意のコマンドを実行できます。 また、セッションは永続的であるため、1 つのコマンドでデータを収集し、後続のコマンドで利用することができます。

たとえば、次のコマンドは $s 変数のセッションで Get-HotFix コマンドを実行し、結果を $h 変数に保存します。 $h 変数は $s のそれぞれのセッションで作成されますが、ローカル セッションには存在しません。

```
Invoke-Command -Session $s {$h = Get-HotFix}
```

これで、次のように、後続のコマンドで $h 変数のデータを使用できます。 結果はローカル コンピューターに表示されます。

```
Invoke-Command -Session $s {$h | where {$_.installedby -ne "NTAUTHORITY\SYSTEM"}}
```

### 高度なリモート処理
Windows PowerShell のリモート管理はこれだけではありません。 Windows PowerShell と共にインストールされるコマンドレットを使用して、ローカルとリモートの両側からのリモート セッションの確立と構成、カスタマイズおよび制限されたセッションの作成、リモート セッションで実際に暗黙的に実行されるコマンドのリモート セッションからのユーザーによるインポート、リモート セッションのセキュリティの構成など、さまざまな処理を実行できます。

リモートの構成を容易にするために、Windows PowerShell には WSMan プロバイダーが含まれます。 プロバイダーが作成する WSMAN: ドライブでは、ローカル コンピューターとリモート コンピューターの構成設定の階層内を移動できます。
WSMan プロバイダーの詳細については、「[WSMan Provider (WSMan プロバイダー)](https://technet.microsoft.com/en-us/library/dd819476.aspx)」および   [WS-Management コマンドレットに関するページ](https://technet.microsoft.com/en-us/library/dd819481.aspx)を参照するか、Windows PowerShell コンソールで「get-help wsman」と入力してください。

詳細については、次のドキュメントをご覧ください。
- [リモートのよく寄せられる質問について](https://technet.microsoft.com/en-us/library/dd315359.aspx)
- [Register-pssessionconfiguration](https://technet.microsoft.com/en-us/library/dd819496.aspx)
- [Import-PSSession](https://technet.microsoft.com/en-us/library/dd347575.aspx) 

リモート処理のエラーに関するヘルプは、「[about_Remote_Troubleshooting](https://technet.microsoft.com/en-us/library/dd347642.aspx)」をご覧ください。

## 参照
- [about_remote に関するページ](https://technet.microsoft.com/en-us/library/9b4a5c87-9162-4adf-bdfe-fbc80b9b8970)
- [about_Remote_FAQ](https://technet.microsoft.com/en-us/library/e23702fd-9415-4a98-9975-390a4d3adc42)
- [about_Remote_Requirements](https://technet.microsoft.com/en-us/library/da213949-134c-4741-b307-81f4492ba1bd)
- [about_Remote_Troubleshooting](https://technet.microsoft.com/en-us/library/2f890148-8578-49ed-85ea-79a489dd6317)
- [about_PSSessions](https://technet.microsoft.com/en-us/library/7a9b4e0e-fa1b-47b0-92f6-6e2995d70acb)
- [about_WS コマンドレットについて](https://technet.microsoft.com/en-us/library/6ed3370a-ea10-45a5-9493-696aeace27ed)
- [呼び出すコマンド](https://technet.microsoft.com/en-us/library/22fd98ba-1874-492e-95a5-c069467b8462)
- [Import-pssession](https://technet.microsoft.com/en-us/library/048c115e-a6fb-4e0d-8cea-c5ca24630c9d)
- [新しい PSSession](https://technet.microsoft.com/en-us/library/59452f12-a11d-4558-99ea-e6ca6ad5ffd3)
- [Register-pssessionconfiguration](https://technet.microsoft.com/en-us/library/af68867a-d201-4b19-a1de-594015ed8a25)
- [WSMan プロバイダー](https://technet.microsoft.com/en-us/library/66fe1241-e08f-49ca-832f-a84c33ca8735)




<!--HONumber=Oct16_HO1-->


