---
title: "32 ビット版の Windows PowerShell を起動する"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 12b31890-2609-4a76-8c24-0ebe78084f50
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 310f898a471ca60da4d9ebe5234ee86a636e44b5

---

# 32 ビット版の Windows PowerShell を起動する
64 ビット コンピューターに Windows PowerShell をインストールする場合、64 ビット版に加えて、**Windows PowerShell (x86)**、つまり Windows PowerShell の 32 ビット版もインストールされます。 Windows PowerShell を実行すると、既定では 64 ビット版が実行されます。

ただし、32 ビット版を必要とするモジュールを使用するときや、32 ビットのコンピューターにリモートで接続しているときなど、**Windows PowerShell (x86)** の実行を必要とすることもときどきあります。

Windows PowerShell の 32 ビット版を起動するには、次の手順のいずれかを使用します。

#### Windows ServerÂ® 2012 r2

-   **[スタート]** 画面で、「**Windows PowerShell (x86)**」と入力します。 **[Windows PowerShell x86]** タイルをクリックします。

-   **サーバー マネージャー**の **[ツール]** メニューから、**[Windows PowerShell (x86)]** を選択します。

-   デスクトップで、右上隅にカーソルを移動し、**[検索]** をクリックする。「**PowerShell x86**」と入力し、**[Windows PowerShell (x86)]** をクリックします。

-   コマンドラインを使用して次のように入力します。 `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows Server 2012 の場合

-   **[スタート]** 画面で、「**PowerShell**」と入力して **[Windows PowerShell (x86)]** をクリックします。

-   **サーバー マネージャー**の **[ツール]** メニューから、**[Windows PowerShell (x86)]** を選択します。

-   デスクトップで、右上隅にカーソルを移動し、**[検索]** をクリックする。「**PowerShell**」と入力し、**[Windows PowerShell (x86)]** をクリックします。

-   コマンドラインを使用して次のように入力します。 `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows 8.1 の場合

-   **[スタート]** 画面で、「**Windows PowerShell (x86)**」と入力します。 **[Windows PowerShell x86]** タイルをクリックします。

-   Windows 8.1 の[リモート サーバー管理ツール](http://go.microsoft.com/fwlink/?LinkID=304145)を実行している場合、**[サーバー管理ツール]** メニューから [Windows PowerShell x86] を開くこともできます。 **[Windows PowerShell (x86)]** を選択します。

-   デスクトップで、右上隅にカーソルを移動し、**[検索]** をクリックする。「**PowerShell x86**」と入力し、**[Windows PowerShell (x86)]** をクリックします。
   
-   コマンドラインを使用して次のように入力します。 `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`

#### Windows 8 の場合

-   **[スタート]** 画面で、右上隅にカーソルを移動し、**[設定]** をクリックし、**[タイル]** をクリックします。そして、**[管理ツールを表示する]** スライダーを [はい] に移動させます。 次に「**PowerShell**」と入力し、**[Windows PowerShell(x86)]** をクリックします。

-   Windows 8 の[リモート サーバー管理ツール](http://www.microsoft.com/download/details.aspx?id=28972)を実行している場合、**[サーバー管理ツール]** メニューから [Windows PowerShell x86] を開くこともできます。 **[Windows PowerShell (x86)]** を選択します。

-   **[スタート]** 画面またはデスクトップで、「**PowerShell (x86)**」と入力し、**[Windows PowerShell (x86)]** をクリックします。

-   コマンドラインを使用して次のように入力します。 `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe`



<!--HONumber=Oct16_HO1-->


