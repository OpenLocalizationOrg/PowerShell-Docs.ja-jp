---
title: "Windows PowerShell のインストール"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 6fbb0409-5a54-48ec-95e6-7f8b7d8c4969
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 00249ec98e1624a949fe11fee8be9e93018578a9

---

# Windows PowerShell のインストール
Windows® 8 と Windows Server® 2012 には、Windows PowerShell 3.0 とその前提条件がすべて含まれています。 システムには、Windows PowerShell 3.0 を使用できないホスト プログラムとの下位互換性のために Windows PowerShell 2.0 エンジンも含まれています。

このトピックでは、以前のシステムに Windows PowerShell 3.0 をインストールし、必要な機能をインストールして有効にする方法を説明します。

このトピックには、次のセクションが含まれています。

-   [Windows 8 および Windows Server 2012 での Windows PowerShell のインストールを実行します。](Installing-Windows-PowerShell.md#BKMK_InstallingOnWindows8andWindowsServer2012)

-   [Windows 7 および Windows Server 2008 R2 で Windows PowerShell のインストール](Installing-Windows-PowerShell.md#BKMK_InstallingOnWindows7andWindowsServer2008R2)

-   [Windows Server 2008 での Windows PowerShell のインストールを実行します。](Installing-Windows-PowerShell.md#BKMK_InstallingOnWindowsServer2008LH)

-   [Server Core で Windows PowerShell のインストール](Installing-Windows-PowerShell.md#BKMK_InstallingOnServerCore)

-   [Windows PowerShell Web Access の展開](https://technet.microsoft.com/en-us/library/639d0eff-98a3-4124-b52c-26921ebd98b0)

-   [Windows PowerShell 2.0 エンジンのインストール](Installing-the-Windows-PowerShell-2.0-Engine.md)

## <a name="BKMK_InstallingOnWindows8andWindowsServer2012"></a>Windows 8 と Windows Server 2012 での Windows PowerShell のインストール
Windows PowerShell 3.0 は最初からインストールと構成が済んでおり、使用準備が整った状態です。 Windows PowerShell Integrated Scripting Environment (ISE) がインストールされ有効になっています。 Windows PowerShell を開始する方法の詳細については、「[Windows 8 で Windows PowerShell を開始する](https://technet.microsoft.com/en-us/library/d7be1668-8617-4890-ad90-dd9765fbd2c3)」と「[Windows Server 2012 で Windows PowerShell を開始する](https://technet.microsoft.com/library/hh831491.aspx#BKMK_powershell)」をご覧ください。

## <a name="BKMK_InstallingOnWindows7andWindowsServer2008R2"></a>Windows 7 と Windows Server 2008 R2 での Windows PowerShell のインストール
次に挙げる手順では、Windows 7 Service Pack 1 と Windows Server 2008 R2 Service Pack 1 を実行するコンピューターに Windows PowerShell 3.0 をインストールする方法を説明します。 その下には、Windows Server 2008 R2 の Server Core インストール オプションを使用して実行するコンピューター用に、インストール手順が別に用意されています。

#### インストールの準備を行う

-   Windows Management Framework 3.0 をインストールする前に、以前のバージョンの Windows Management Framework 3.0 をすべてアンインストールします。

#### Windows PowerShell 3.0 をインストールするには

1.  Microsoft .NET Framework 4 (dotNetFx40_Full_setup.exe) を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=212547](http://go.microsoft.com/fwlink/?LinkID=212547)) から完全インストールします。

    または、Microsoft .NET Framework 4.5 (dotNetFx45_Full_setup.exe) を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=242919](http://go.microsoft.com/fwlink/?LinkID=242919)) からインストールします。

2.  Windows Management Framework 3.0 を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=240290](http://go.microsoft.com/fwlink/?LinkID=240290)) からインストールします。

Windows PowerShell 3.0 を開始する方法の詳細については、「[以前のバージョンの Windows で Windows PowerShell を開始する](Starting-Windows-PowerShell-on-Earlier-Versions-of-Windows.md)」をご覧ください。

## <a name="BKMK_InstallingOnServerCore"></a>Server Core での Windows PowerShell のインストール
次に挙げる手順では、Windows Server 2008 R2 Service Pack 1 の Server Core インストール オプションを実行するコンピューターに Windows PowerShell 3.0 をインストールする方法を説明します。

手順の最初の操作では、展開イメージのサービスと管理 (DISM) コマンドを使用して、Server Core 対応 Microsoft .NET Framework 2.0 と Windows PowerShell 2.0 をインストールします。 これらのプログラムは、以降の操作でインストールする Windows Management Framework 3.0 の前提条件です。

#### インストールの準備を行う

-   Windows Management Framework 3.0 をインストールする前に、以前のバージョンの Windows Management Framework 3.0 をすべてアンインストールします。

#### Windows PowerShell 3.0 をインストールするには

1.  Cmd.exe を起動します

2.  次の DISM コマンドを実行します。 これらのコマンドにより、.NET Framework 2.0 と Windows PowerShell 2.0 がインストールされます。

    ```
    dism /online /enable-feature:NetFx2-ServerCore
    dism /online /enable-feature:MicrosoftWindowsPowerShell
    dism /online /enable-feature:NetFx2-ServerCore-WOW64
    ```

3.  Server Core 対応 Microsoft .NET Framework 4.0 を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=248450](http://go.microsoft.com/fwlink/?LinkID=248450)) から完全インストールします。

4.  Windows Management Framework 3.0 を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=240290](http://go.microsoft.com/fwlink/?LinkID=240290)) からインストールします。

## <a name="BKMK_InstallingOnWindowsServer2008LH"></a>Windows Server 2008 での Windows PowerShell のインストール
次に挙げる手順では、Windows Server 2008 Service Pack 2 を実行するコンピューターに Windows PowerShell 3.0 をインストールする方法を説明します。

Windows Server 2008 システムでは、Windows Management Framework (Windows PowerShell 2.0、KB 968930) が Windows Management Framework 3.0 の前提条件となっています。 "認証の拡張保護" 機能を使用すると、コンピューターを認証転送攻撃から保護したり、リモート セッションの作成時に **UseSSL** パラメーターを使用したりできます。 Windows PowerShell 3.0 と Windows PowerShell 2.0 エンジンをインストールするには、次の手順を実行します。

#### インストールの準備を行う

-   Windows Management Framework 3.0 をインストールする前に、以前のバージョンの Windows Management Framework 3.0 をすべてアンインストールします。

#### Windows PowerShell 3.0 をインストールするには

1.  Microsoft .NET Framework 3.5 Service Pack 1 を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=242910](http://go.microsoft.com/fwlink/?LinkID=242910)) からインストールします。

2.  Windows Management Framework (Windows PowerShell 2.0、KB 968930) を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkId=243035](http://go.microsoft.com/fwlink/?LinkId=243035)) からインストールします。

3.  Microsoft .NET Framework 4 (dotNetFx40_Full_setup.exe) を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=212547](http://go.microsoft.com/fwlink/?LinkID=212547)) から完全インストールします。

    または、Microsoft .NET Framework 4.5 (dotNetFx45_Full_setup.exe) を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=242919](http://go.microsoft.com/fwlink/?LinkID=242919)) からインストールします。

4.  "認証の拡張保護" (KB 968389) を [http://go.microsoft.com/fwlink/?LinkID=186398](http://go.microsoft.com/fwlink/?LinkID=186398) からインストールします。

5.  Windows Management Framework 3.0 を Microsoft ダウンロード センター ([http://go.microsoft.com/fwlink/?LinkID=240290](http://go.microsoft.com/fwlink/?LinkID=240290)) からインストールします。

## 参照
[Windows PowerShell のシステム要件](Windows-PowerShell-System-Requirements.md)
[Windows PowerShell [ps] の開始](https://technet.microsoft.com/en-us/library/8ec8c2d7-8e7c-4722-a3d2-498fe5739a8e)



<!--HONumber=Oct16_HO1-->


