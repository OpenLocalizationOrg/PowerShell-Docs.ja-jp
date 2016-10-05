---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "複数のコンピューターの展開と保守"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 784806197a64eb30af1ecea4af55575434ce7b87

---

# 複数のコンピューターの展開と保守
この時点で、JEA をローカル システムに何回か展開しています。
運用環境はおそらく複数のコンピューターで構成されているため、展開プロセスの重要な手順に従って、各コンピューターでこれらを繰り返すことが重要です。

## 大まかな手順:
1.  モジュールを (ロール機能により) 各ノードにコピーします。
2.  セッション構成ファイルを各ノードにコピーします。
3.  セッション構成を使用して `Register-PSSessionConfiguration` を実行します。
4.  セッション構成とツールキットのコピーを安全な場所に保管します。
変更を行う際は、"信頼できる唯一の情報源" を採用することをお勧めします。

## スクリプトの例
配置のスクリプトの例を次に示します。
これを自身の環境で使用する場合、実際のファイル共有とモジュールの名前とパスを使用する必要があります。
```PowerShell
# First, copy the session configuration and modules (containing role capability files) onto a file share you have access to.
Copy-Item -Path 'C:\Demo\Demo.pssc' -Destination '\\FileShare\JEA\Demo.pssc'
Copy-Item -Path 'C:\Program Files\WindowsPowerShell\Modules\SomeModule\' -Recurse -Destination '\\FileShare\JEA\SomeModule'

# Next, author a setup script (C:\JEA\Deploy.ps1) to run on each individual node
    # Contents of C:\JEA\Deploy.ps1
    New-Item -ItemType Directory -Path C:\JEADeploy
    Copy-Item -Path '\\FileShare\JEA\Demo.pssc' -Destination 'C:\JEADeploy\'
    Copy-Item -Path '\\FileShare\JEA\SomeModule' -Recurse -Destination 'C:\Program Files\WindowsPowerShell\Modules' # Remember, Role Capability Files are found in modules
    if (Get-PSSessionConfiguration -Name JEADemo -ErrorAction SilentlyContinue)
    {
        Unregister-PSSessionConfiguration -Name JEADemo -ErrorAction Stop
    }

    Register-PSSessionConfiguration -Name JEADemo -Path 'C:\JEADeploy\Demo.pssc'
    Remove-Item -Path 'C:\JEADeploy' # Don't forget to clean up!

# Now, invoke the script on all of the target machines.
# Note: this requires PowerShell Remoting be enabled on each machine. Enabling PowerShell remoting is a requirement to use JEA as well.
# You may need to provide the "-Credential" parameter if your current user account does not have admin permissions on these machines.
Invoke-Command –ComputerName 'Node1', 'Node2', 'Node3', 'NodeN' -FilePath 'C:\JEA\Deploy.ps1'

# Finally, delete the session configuration and role capability files from the file share.
Remove-Item -Path '\\FileShare\JEA\Demo.pssc'
Remove-Item -Path '\\FileShare\JEA\SomeModule' -Recurse
```
## 機能の変更
多くのコンピューターを扱う際、一貫した方法で変更を反映させることが重要です。
JEA に DSC リソースがあれば、環境を同期することができます。
それまでは、セッション構成のマスター コピーを保持し、変更を加えるたびに再展開することを強くお勧めします。

## 機能の削除
JEA 構成をシステムから削除するには、各コンピューターで次のコマンドを使用します。
```PowerShell
Unregister-PSSessionConfiguration -Name JEADemo
```




<!--HONumber=Oct16_HO1-->


