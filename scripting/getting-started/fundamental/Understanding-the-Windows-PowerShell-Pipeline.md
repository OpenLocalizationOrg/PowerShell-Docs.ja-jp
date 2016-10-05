---
title: "Windows PowerShell パイプラインを理解する"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 6be50926-7943-4ef7-9499-4490d72a63fb
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d90bf940a1047b629f7b59d239aab50a78748251

---

# Windows PowerShell パイプラインを理解する
Windows PowerShell では、パイプ処理をほぼすべての状況で使用できます。 画面にはテキストが表示されますが、コマンド間で受け渡しされる情報はテキストではありません。 パイプの中を流れるデータは、実際にはオブジェクトです。

パイプラインで使う表記は他のシェルで使う表記に似ているため、一見すると、Windows PowerShell で何かが新しく導入されたことがわかりにくい可能性があります。 たとえば、**Out-Host** コマンドレットを使用して、別のコマンドからの出力をページ単位で表示させると、通常のテキストがページごとに分割されて画面上に表示されているだけのように見えます。

```
PS> Get-ChildItem -Path C:\WINDOWS\System32 | Out-Host -Paging

    Directory: Microsoft.Windows PowerShell.Core\FileSystem::C:\WINDOWS\system32

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---        2005-10-22  11:04 PM        315 $winnt$.inf
-a---        2004-08-04   8:00 AM      68608 access.cpl
-a---        2004-08-04   8:00 AM      64512 acctres.dll
-a---        2004-08-04   8:00 AM     183808 accwiz.exe
-a---        2004-08-04   8:00 AM      61952 acelpdec.ax
-a---        2004-08-04   8:00 AM     129536 acledit.dll
-a---        2004-08-04   8:00 AM     114688 aclui.dll
-a---        2004-08-04   8:00 AM     194048 activeds.dll
-a---        2004-08-04   8:00 AM     111104 activeds.tlb
-a---        2004-08-04   8:00 AM       4096 actmovie.exe
-a---        2004-08-04   8:00 AM     101888 actxprxy.dll
-a---        2003-02-21   6:50 PM     143150 admgmt.msc
-a---        2006-01-25   3:35 PM      53760 admparse.dll
<SPACE> next page; <CR> next line; Q quit
...
```

Out-Host -Paging コマンドは、長大な出力結果を少しずつ表示したい場合に役立つパイプライン要素です。 特に、CPU への負荷がきわめて大きい処理で使用すると便利です。 処理は 1 ページ分を表示する準備が整った段階で Out-Host コマンドレットに渡されるため、パイプラインにおける直前のコマンドレットは、次のページの出力の用意ができるまで処理を中断します。 このことは、Windows PowerShell による CPU およびメモリの使用状況を Windows タスク マネージャーで監視するとわかります。

次のコマンドを実行します。 **Get-childitem C:\\Windows-Recurse**します。 このコマンドに CPU とメモリの使用状況の比較: **Get-childitem C:\\Windows-Recurse |Out-host-Paging**します。 画面に表示されるのはテキストですが、これは、コンソール ウィンドウに表示するためには、オブジェクトをテキストで表す必要があるためです。 これは、Windows PowerShell 内部で実際に行われていることを表現するための 1 つの手段にすぎません。 Get-Location コマンドレットを例に考えてみます。 現在の場所が C ドライブのルートである場合に、**Get-Location** と入力すると、次のような出力結果が表示されます。

```
PS> Get-Location

Path
----
C:\
```

パイプラインを流れるデータがテキストだとした場合、**Get-Location | Out-Host** というコマンドを実行すると、**Get-Location** から **Out-Host** には、一連の文字が、画面に表示される順番で渡されます。 つまり見出しの情報を無視する場合は、 **Out-host** 文字が最初に受信 '**C'**, 、文字、'**:'**, 、文字、'**\\'**します。 **Out-Host** コマンドレットは、**Get-Location** コマンドレットによって出力された文字にどの意味を関連付けるかを判断できません。

Windows PowerShell では、パイプラインのコマンド間でデータを受け渡すのに、テキストではなくオブジェクトが使用されています。 ユーザーの立場から見ると、関連する情報がオブジェクトとしてパッケージ化されることにより、情報が扱いやすい単位でまとめられ、必要に応じて特定の項目だけを抽出できるという利点があります。

**Get-Location** コマンドは、現在のパスを表すテキストを返すわけではありません。 実際に返されるのは、**PathInfo** オブジェクトという情報のパッケージです。このオブジェクトには、現在のパスだけでなく、他の情報も一緒に格納されています。 この **PathInfo** オブジェクトが **Out-Host** コマンドレットによって画面に送られると、Windows PowerShell は、どの情報をどのように表示するかを書式ルールに基づいて決定します。

実際、**Get-Location** コマンドレットによって出力される見出し情報は、処理の最後になって初めて、画面表示用にデータを書式設定するプロセスの一部として追加されます。 ユーザーが画面で確認できるのは情報の要約であり、出力されたオブジェクトの完全な表現ではありません。

Windows PowerShell のコマンドから、コンソール ウィンドウに表示されている以上の情報が出力されているとすれば、見えない要素を取得するにはどうすればよいのかという疑問が残ります。 つまり、残りのデータを表示するにはどうすればよいか、 また、Windows PowerShell で通常使用される表示形式とは異なる形式でデータを表示するにはどうすればよいのかという点です。

この章の残りの部分では、特定の Windows PowerShell オブジェクトの構造を調べる方法、特定の項目を選んで見やすいように書式化する方法、この情報を他の出力場所 (ファイルやプリンターなど) に送る方法について説明します。




<!--HONumber=Oct16_HO1-->


