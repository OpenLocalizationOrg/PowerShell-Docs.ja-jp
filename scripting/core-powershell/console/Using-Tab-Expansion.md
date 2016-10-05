---
title: "タブ拡張の使用"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: c8730471-bf6a-43b8-ab1d-f9ef5a74f04e
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: fd950429d80972b6025e67ea727aa49ef195e882

---

# タブ拡張の使用
コマンド ライン シェルは多くの場合、長いファイルやコマンドの名前を自動補完する手段を備えており、コマンド入力を迅速化したり、ヒントを与えたりします。 Windows PowerShell では、**Tab** キーを押してファイル名やコマンドレット名を入力できます。

> [!NOTE]
> タブ拡張は、内部関数 TabExpansion によって制御されています。 この関数は変更やオーバーライドができるため、この説明は既定の Windows PowerShell 構成の動作についてのガイドとなっています。

使用可能な選択肢からファイル名またはパスを自動的に入力するには、名前の一部を入力して **Tab** キーを押します。 Windows PowerShell は自動的に名前を拡張し、最初に見つかった一致する項目を表示します。 **Tab** キーを繰り返し押すと、すべての利用可能な選択肢が順番に表示されます。

コマンドレット名のタブ拡張は若干異なります。 コマンドレット名にタブ拡張を使用するには、名前の最初の部分全体 (動詞) とそれに続くハイフンを入力します。 部分一致するよう、名前をより詳細に入力することもできます。 たとえば、**get-co** と入力してから **Tab** キーを押した場合、Windows PowerShell はこれを自動的に **Get-Command** コマンドレットに拡張します (文字の大文字と小文字も標準形式に変更されることにも注目してください)。 もう一度 **Tab** キーを押すと、Windows PowerShell はこれを置き換えて、もう 1 つだけある一致するコマンドレット名 **Get-Content** を表示します。

同じ行にタブ拡張を繰り返し使用できます。 たとえば、次のように入力すると **Get-Content** コマンドレットの名前にタブ拡張を使用できます。

```
PS> Get-Con<Tab>
```

**Tab** キーを押すと、コマンドは次のように拡張されます。

```
PS> Get-Content
```

次に、アクティブ セットアップのログ ファイルのパスを部分的に指定し、もう一度タブ拡張を使用できます。

```
PS> Get-Content c:\windows\acts<Tab>
```

**Tab** キーを押すと、コマンドは次のように拡張されます。

```
PS> Get-Content C:\windows\actsetup.log
```

> [!NOTE]
> タブ拡張プロセスの 1 つの制限は、タブがあると、必ず単語の補完を試みていると解釈されることです。 コマンドの例をコピーして Windows PowerShell コンソールに貼り付ける場合は、サンプルにタブが含まれていないことをご確認ください。タブが含まれていると結果は予測できず、ほぼ意図したとおりにはなりません。




<!--HONumber=Oct16_HO1-->


