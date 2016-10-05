---
title: "Windows PowerShell 2.0 エンジンを開始する"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: edafc2fa-7576-49c2-bbba-9336f4bcfc28
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 094c3c9f240457fc884031e7d82dcdc1e81e582d

---

# Windows PowerShell 2.0 エンジンを開始する
このセクションでは、Windows PowerShell 2.0 エンジンがインストールされている Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、および Windows PowerShell 2.0、Windows PowerShell 3.0、Windows PowerShell 4.0 がインストールされているその他のシステムで、Windows PowerShell 2.0 エンジンを開始する方法について説明します。

Windows PowerShell 4.0 および Windows PowerShell 3.0 は、Windows PowerShell 2.0 との下位互換性を保つように設計されています。 Windows PowerShell 2.0 と Windows PowerShell 3.0 用に記述されたコマンドレット、プロバイダー、スナップイン、モジュール、スクリプトは、未変更のまま Windows PowerShell 4.0 で実行できます。 ただし、Microsoft .NET Framework 4 でランタイムのアクティブ化ポリシーが変更されたため、Windows PowerShell 2.0 用に記述され、共通言語ランタイム (CLR) 2.0 でコンパイルされた Windows PowerShell ホスト プログラムは、変更を加えなければ Windows PowerShell 3.0 または Windows PowerShell 4.0 (CLR 4.0 でコンパイルされたもの) で実行できません。 Windows PowerShell 2.0 エンジンは、既存のスクリプトまたはホスト プログラムが Windows PowerShell 4.0、Windows PowerShell 3.0、Microsoft .NET Framework 4 との互換性がないために実行できない場合に限り使用することを意図しています。 そのようなケースはまれと考えられます。

Windows PowerShell 2.0 エンジンを必要とする多くのプログラムは、エンジンを自動的に起動します。 次の手順は、エンジンを手動で起動する必要のあるまれな状況のためのものです。

## 必要なプログラムのインストールと有効化
Windows PowerShell 2.0 エンジンを起動する前に、Windows PowerShell 2.0 エンジンと Microsoft .NET Framework 3.5 Service Pack 1 を有効にします。 方法については、「[Windows PowerShell のインストール](Installing-Windows-PowerShell.md)」をご覧ください。

[Windows Management Framework 4.0](http://go.microsoft.com/fwlink/?LinkID=293881) または Windows Management Framework 3.0 がインストールされているシステムには、すべての必要なコンポーネントが揃っています。 これ以上の構成は必要ありません。 [Windows Management Framework 4.0](http://go.microsoft.com/fwlink/?LinkID=293881) または Windows Management Framework 3.0 のインストール方法の詳細については、「[Windows PowerShell のインストール](Installing-Windows-PowerShell.md)」をご覧ください。

## Windows PowerShell 2.0 エンジンを起動する方法
Windows PowerShell を起動すると、既定で最新バージョンが起動します。 Windows PowerShell 2.0 エンジンで Windows PowerShell を起動するには、PowerShell.exe の Version パラメーターを使用します。 コマンドは、Windows PowerShell と Cmd.exe を含む任意のコマンド プロンプトで実行できます。

```
PowerShell.exe -Version 2
```

## Windows PowerShell 2.0 エンジンを使用してリモート セッションを開始する方法
リモート セッションで Windows PowerShell 2.0 エンジンを実行するには、Windows PowerShell 2.0 エンジンを読み込むリモート コンピューターでセッション構成 ("エンドポイント" とも呼ばれる) を作成します。 セッション構成は、リモート コンピューターに保存され、許可されているユーザーは誰でもそれを使って Windows PowerShell 2.0 エンジンを使用するセッションを作成できます。

これは、通常システム管理者によって実行される高度なタスクです。

次の手順では、[Register-PSSessionConfiguration](https://technet.microsoft.com/en-us/library/e9152ae2-bd6d-4056-9bc7-dc1893aa29ea) コマンドレットの **PSVersion** パラメーターを使用して、Windows PowerShell 2.0 エンジンを使用するセッション構成を作成します。 [New-PSSessionConfigurationFile](https://technet.microsoft.com/en-us/library/5f3e3633-6e90-479c-aea9-ba45a1954866) コマンドレットの **PowerShellVersion** パラメーターを使用して、Windows PowerShell 2.0 エンジンを読み込むセッションのセッション構成ファイルを作成することも、[Set-PSSessionConfiguration](https://technet.microsoft.com/en-us/library/b21fbad3-1759-4260-b206-dcb8431cd6ea) パラメーターの **PSVersion** パラメーターを使用して、Windows PowerShell 2.0 エンジンを使用するようにセッション構成を変更することもできます。

セッション構成ファイルの詳細については、「[about_Session_Configuration_Files](https://technet.microsoft.com/en-us/library/c7217447-1ebf-477b-a8ef-4dbe9a1473b8)」をご覧ください。セットアップとセキュリティを含むセッション構成の詳細については、「[about_Session_Configurations [v4]](https://technet.microsoft.com/en-us/library/a2fbe12a-350c-4d04-be50-24102824e3ab)」をご覧ください。

#### リモートの Windows PowerShell 2.0 セッションを開始するには

1.  Windows PowerShell 2.0 エンジンを必要とするセッション構成を作成するには、**PSVersion** パラメーターに値 "2.0" を指定して [Register-PSSessionConfiguration](https://technet.microsoft.com/en-us/library/e9152ae2-bd6d-4056-9bc7-dc1893aa29ea) コマンドレットを使用します。 接続の "サーバー側" (受信側) にあるコンピューターでこのコマンドを実行します。

    次のサンプル コマンドは、Server01 コンピューター上に PS2 セッション構成を作成します。 このコマンドを実行するには、**[管理者として実行]** オプションを使用して Windows PowerShell 4.0 または Windows PowerShell 3.0 を起動します。

    ```
    Register-PSSessionConfiguration -Name PS2 -PSVersion 2.0
    ```

2.  PS2 セッション構成を使用する Server01 コンピューターでセッションを作成するには、リモート セッションを作成するコマンドレット ([New-PSSession](https://technet.microsoft.com/en-us/library/76f6628c-054c-4eda-ba7a-a6f28daaa26f) コマンドレットなど) の **ConfigurationName** パラメーターを使用します。

    セッション構成を使用するセッションを開始すると、Windows PowerShell 2.0 エンジンが自動的にセッションに読み込まれます。

    次のコマンドは、PS2 セッション構成を使用する Server01 コンピューターでセッションを開始します。 このコマンドは、セッションを $s 変数に保存します。

    ```
    $s = New-PSSession -ComputerName Server01 -ConfigurationName PS2
    ```

## Windows PowerShell 2.0 エンジンを使用してバックグラウンド ジョブを開始する方法
Windows PowerShell 2.0 エンジンを使用してバックグラウンド ジョブを開始するには、[Start-Job](https://technet.microsoft.com/en-us/library/2bc04935-0deb-4ec0-b856-d7290cca6442) コマンドレットの **PSVersion** パラメーターを使用します。

次のコマンドは、Windows PowerShell 2.0 エンジンを使用してバックグラウンド ジョブを開始します。

```
Start-Job {Get-Process} -PSVersion 2.0
```

バックグラウンド ジョブの詳細については、「[about_Jobs [v4]](https://technet.microsoft.com/en-us/library/7362512a-8a4e-4575-b2ea-a740e5c4f002)」をご覧ください。




<!--HONumber=Oct16_HO1-->


