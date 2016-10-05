---
title: "以前のバージョンの Windows で Windows PowerShell を開始する"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 57125436-3d1e-4e7f-b5c4-8f0ecb49d642
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 6cdb6bb5d901c9bc7d2b7f5051e372337bbb69f9

---

# 以前のバージョンの Windows で Windows PowerShell を開始する
このセクションでは、WindowsÂ® 7、Windows ServerÂ® 2008 R2 および Windows Server 2008 で Windows PowerShell および Windows PowerShell Integrated Scripting Environment (ISE) を開始する方法について説明します。 Windows ServerÂ® 2008 R2 および Windows Server 2008 での Windows PowerShell 2.0 での Windows PowerShell ISE のオプションの機能を有効にする方法についても説明します。

サポートされるシステムで Windows PowerShell 4.0 をインストールするには、[Windows Management Framework 4.0](http://go.microsoft.com/fwlink/?LinkID=293881) をダウンロードしてインストールします。 詳しくは、「[Windows PowerShell のインストール](Installing-Windows-PowerShell.md)」をご覧ください。

サポートされるシステムで Windows PowerShell 3.0 をインストールするには、[Windows Management Framework 3.0](http://go.microsoft.com/fwlink/?LinkID=240290) をダウンロードしてインストールします。 詳しくは、「[Windows PowerShell のインストール](Installing-Windows-PowerShell.md)」をご覧ください。

## 以前のバージョンの Windows で Windows PowerShell を開始する方法
次のいずれかの方法で、必要に応じて、Windows PowerShell 3.0 または Windows PowerShell 4.0 のインストールされたバージョンを開始します。

#### [スタート] メニューからの場合

-   **[スタート]** をクリックし、「**PowerShell**」と入力して **[Windows PowerShell]** をクリックします。

-   **[スタート]** メニューから、**[スタート]**、**[すべてのプログラム]**、**[アクセサリ]**、**[Windows PowerShell]** フォルダー、**[Windows PowerShell]** の順にクリックします。

#### コマンド プロンプトでの場合

-   Cmd.exe、Windows PowerShell、または Windows PowerShell ISE で Windows PowerShell を開始するには、次のように入力します。

    ```
    PowerShell
    ```

    また、PowerShell.exe プログラムのパラメーターを使って、セッションをカスタマイズできます。 詳しくは、「[PowerShell.exe コマンドラインのヘルプ](../core-powershell/console/PowerShell.exe-Command-Line-Help.md)」をご覧ください。

#### 管理特権を使う場合 ("管理者として実行")

1.  **[スタート]** をクリックし、「**PowerShell**」と入力し、**[Windows PowerShell]** を右クリックして、**[管理者として実行]** をクリックします。

## 以前のリリースの Windows で Windows PowerShell ISE を開始する方法
次のいずれかの方法で Windows PowerShell ISE を開始します。

#### [スタート] メニューからの場合

-   **[スタート]** をクリックし、「**ISE**」と入力して **[Windows PowerShell ISE]** をクリックします。

-   **[スタート]** メニューから、**[スタート]**、**[すべてのプログラム]**、**[アクセサリ]**、**[Windows PowerShell]** フォルダー、**[Windows PowerShell ISE]** の順にクリックします。

#### コマンド プロンプトでの場合

-   Cmd.exe、Windows PowerShell、または Windows PowerShell ISE で Windows PowerShell を開始するには、次のように入力します。

    ```
    PowerShell_ISE
    ```

    または

    ```
    ISE
    ```

#### 管理特権を使う場合 ("管理者として実行")

1.  **[スタート]** をクリックし、「**ISE**」と入力し、**[Windows PowerShell ISE]** を右クリックして、**[管理者として実行]** をクリックします。

## 以前のリリースの Windows で Windows PowerShell ISE を有効にする方法
Windows PowerShell 4.0 および Windows PowerShell 3.0 では、Windows PowerShell ISE は既定ではすべてのバージョンの Windows で有効になっています。 まだ有効になっていない場合、Windows Management Framework 4.0 または Windows Management Framework 3.0 により有効になります。

Windows PowerShell 2.0 では、Windows PowerShell ISE は既定では Windows 7 で有効になっています。 ただし、Windows Server 2008 R2 および Windows Server 2008 では省略可能な機能です。

Windows Server 2008 R2 または Windows Server 2008 で Windows PowerShell 2.0 の Windows PowerShell ISE を有効にするには、次の手順に従います。

#### Windows PowerShell Integrated Scripting Environment (ISE) を有効にするには

1.  サーバー マネージャーを起動します。

2.  **[機能]**、**[機能の追加]** の順にクリックします。

3.  [機能の選択]で、[Windows PowerShell Integrated Scripting Environment (ISE)] をクリックします。




<!--HONumber=Oct16_HO1-->


