---
title: "Windows PowerShell ISE でコンソール ウィンドウを使用する方法"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 44d67705-87c7-4a69-a53e-6471fdebb757
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4588577c0c05fadee6b443e2fa4c32049cf99ac3

---

# Windows PowerShell ISE でコンソール ウィンドウを使用する方法
Windows PowerShell® Integrated Scripting Environment (ISE) のコンソール ウィンドウは、スタンドアロンの Windows PowerShell ISE コンソール ウィンドウとまったく同じ動作をします。

コンソール ウィンドウでコマンドを実行するには、コマンドを入力し、Enter キーを押します。 順番に実行する複数のコマンドを入力するには、コマンドとコマンドの間で SHIFT キーを押しながら ENTER キーを押します。 コマンドを入力する際の支援機能については、「[スクリプト ウィンドウとコンソール ウィンドウでタブ補完を使用する方法](How-to-Use-Tab-Completion-in-the-Script-Pane-and-Console-Pane.md)」をご覧ください。

コマンドを停止するには、ツール バーで **[実行を中止]** をクリックするか、または CTRL + BREAK キーを押します。 コンテキストが明確な場合は、コマンドを停止するために CTRL + C キーを押すこともできます。 たとえば、現在のウィンドウでテキストが選択されている場合、CTRL + C キーはコピー操作にマップされます。

Windows PowerShell v3 以降で、出力ウィンドウがコンソール ウィンドウと結合されました。 これの利点は、スタンドアロンの Windows PowerShell コンソールと同様の動作になったことと、この 2 つが独立していたときに必要だった手順の違いが解消されたことです。 次の操作を行います。

-   コンソール ウィンドウからテキストを選択してクリップボードにコピーし、他の任意のウィンドウに貼り付けます。 テキストを選択するには、出力ウィンドウでマウスをクリックし、マウス ボタンを押したまま、取り込みたいテキスト上をドラッグします。 また、**Shift** キーを押しながら方向キーを押して、テキストを選択することもできます。 その後、CTRL+ C キーを押すか、ツール バーの **[コピー]** アイコンをクリックします。

-   選択したテキストを現在のカーソル位置に貼り付けます。 ツール バーの **[貼り付け]** アイコンをクリックします。

-   コンソール ウィンドウ内のすべてのテキストをクリアします。 コンソール ウィンドウをクリアするには、ツール バーの **[コンソール ウィンドウのクリア]** アイコンをクリックするか、コマンド **Clear-Host** またはそのエイリアスである **cls** を実行します。

## 参照
[Windows PowerShell ISE を使用します。](Using-the-Windows-PowerShell-ISE.md)




<!--HONumber=Oct16_HO1-->


