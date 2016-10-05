---
title: "使い慣れたコマンド名の使用"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 021e2424-c64e-4fa5-aa98-aa6405758d5d
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: b567510b9a39bfa62e64e62752a1a7d2002cf6b9

---

# 使い慣れたコマンド名の使用
Windows PowerShell では、*エイリアス*というメカニズムにより、ユーザーは代替名でコマンドを表せます。 エイリアスがあることで、他のシェルの経験のあるユーザーは、既に知っている一般的なコマンド名を再使用して、Windows PowerShell でも同様の操作を行えます。 Windows PowerShell のエイリアスについて詳しくは解説しませんが、Windows PowerShell の入門段階でもエイリアスを使用できます。

エイリアスは、ユーザーが入力したコマンド名を別のコマンドに関連付けます。 たとえば、出力ウィンドウをクリアする **Clear-Host** という内部関数が Windows PowerShell にあります。 **cls** または **clear** のどちらかのコマンドをコマンド プロンプトで入力すると、Windows PowerShell はそれを **Clear-Host** 関数のエイリアスであると解釈し、**Clear-Host** 関数を実行します。

この機能は、ユーザーが Windows PowerShell を習得するうえで役立ちます。 何よりもまず、Cmd.exe および UNIX ユーザーのほとんどは、幅広いレパートリーのコマンドの名前を既に知っています。相当する Windows PowerShell コマンドがまったく同じ結果を生成するとは限りませんが、形の上ではほぼ同じです。したがって、ユーザーは Windows PowerShell での名前を最初に覚えなくても、既に知っているコマンド名を使用できます。 第 2 に、別のシェルを既に使い慣れているユーザーが新しいシェルを習得する際に感じるフラストレーションの主な原因は、"指が覚えていること" によって引き起こされるエラーです。 Cmd.exe を長年使用しているユーザーであれば、画面が出力でいっぱいになったために画面をクリーンアップしたい場合は、無意識に **cls** コマンドを入力して ENTER キーを押すでしょう。 Windows PowerShell に **Clear-Host** 関数のエイリアスがなければ、"**'cls' is not recognized as a cmdlet, function, operable program, or script file.**" というエラー メッセージが表示されるだけで、 出力をクリアするにはどうすればよいかまったく分からないまま置き去りにされてしまいます。

Windows PowerShell 内で使用できる一般的な Cmd.exe および UNIX コマンドの簡潔な一覧を次に示します。

|||||
|-|-|-|-|
|cat|dir|マウント|rm|
|cd|echo|move|rmdir|
|chdir|erase|popd|sleep|
|clear|h|ps|sort|
|cls|history|pushd|tee|
|copy|kill|pwd|型|
|del|lp|r|write|
|diff|ls|ren||

これらのコマンドのいずれかを無意識に使っていたことに気づいたが、Windows PowerShell ネイティブ コマンドの実名を知りたいという場合は、**Get-Alias** コマンドを使用できます。

```
PS> Get-Alias cls

CommandType     Name                            Definition
-----------     ----                            ----------
Alias           cls                             Clear-Host
```

Windows PowerShell ユーザー ガイドでは一般に、例を読みやすくするために、エイリアスの使用は避けています。 しかし、早い段階でエイリアスについて詳しく知ることは、別のソースにある Windows PowerShell コードの不特定のスニペットを扱う場合や、独自のエイリアスを定義する場合にも役立ちます。 このセクションの残りの部分で、標準エイリアスについて、および独自のエイリアスを定義する方法について説明します。

### 標準エイリアスの解釈
他のインターフェイスとの名前の互換性を目的とした上記のエイリアスとは異なり、Windows PowerShell に組み込まれたエイリアスは一般に、簡潔さを目的に設計されています。 こうした短い名前はすばやく入力できますが、何を表しているかが分かっていなければ、意味を読み取ることはできません。

Windows PowerShell では、一般的な動詞および名詞を表す省略名に基づいた標準エイリアスのセットを定義することで、明確さと簡潔さの妥協を図っています。 これにより、一般的なコマンドレットについて、省略名でその意味を読み取ることができるエイリアスのコア セットが可能になっています。 たとえば、標準エイリアスでは、動詞 **Get** は **g** に、動詞 **Set** は **s** に、名詞 **Item** は **i** に、名詞 **Location** は **l** に、名詞 Command は **cm** にそれぞれ省略されます。

どういうことになるか簡単な例で説明します。 Get-Item の標準エイリアスは、Get の **g** と Item の **i** の組み合わせから、**gi** になっています。 Set-Item の標準エイリアスは、Set の **s** と Item の **i** の組み合わせから、**si** になっています。 Get-Location の標準エイリアスは、Get の **g** と Location の **l** の組み合わせから、**gl** になっています。 Set-Location の標準エイリアスは、Set の **s** と Location の **l** の組み合わせから、**sl** になっています。 Get-Command の標準エイリアスは、Get の **g** と Command の **cm** の組み合わせから、**gcm** になっています。 Set-Command コマンドレットはありませんが、あるとすれば、その標準エイリアスは、Set の **s** と Command の **cm** から **scm** になると推測できます。 さらに、Windows PowerShell エイリアスを使い慣れたユーザーであれば、**scm** を目にしたら、そのエイリアスは Set-Command を表していると推測できるでしょう。

### 新しいエイリアスの作成
Set-Alias コマンドレットを使用して、独自のエイリアスを作成できます。 たとえば、次のステートメントは、「標準エイリアスの解釈」で説明した標準コマンドレット エイリアスを作成します。

```
Set-Alias -Name gi -Value Get-Item
Set-Alias -Name si -Value Set-Item
Set-Alias -Name gl -Value Get-Location
Set-Alias -Name sl -Value Set-Location
Set-Alias -Name gcm -Value Get-Command
```

Windows PowerShell はこうしたコマンドを起動時に内部で使用しますが、これらのエイリアスは変更できません。 これらのコマンドのいずれかを実際に実行しようとすると、エイリアスは変更できないことを示すエラーが表示されます。 たとえば、次のように入力します。

<pre>PS> Set-Alias -Name gi -Value Get-Item Set-Alias : エイリアスは書き込み可能ではありません。エイリアス gi は読み取り専用または定数であり、書き込めません。
At line:1 char:10 + Set-Alias  <<<< -Name gi -Value Get-Item</pre>




<!--HONumber=Oct16_HO1-->


