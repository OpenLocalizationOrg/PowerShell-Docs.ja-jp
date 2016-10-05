---
title: "Out-* コマンドレットを使用したデータのリダイレクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 2a4acd33-041d-43a5-a3e9-9608a4c52b0c
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e62ae14e4d7334d0cd42681c7e2591692c089187

---

# Out-* コマンドレットを使用してデータをリダイレクトする
Windows PowerShell には、データ出力を直接制御できるようにするコマンドレットがいくつかあります。 これらのコマンドレットには、2 つの重要な特性があります。

まず、これらのコマンドレットは、通常、データをいくつかの形式のテキストに変換します。 コマンドレットは、テキスト入力を必要とするシステム コンポーネントにデータを出力するので、変換を実行します。 これは、コマンドレットがオブジェクトを文字列として表現する必要があることを意味します。 したがって、テキストは Windows PowerShell のコンソール ウィンドウに表示される形式に書式設定されます。

2 番目に、これらのコマンドレットは、Windows PowerShell から他の場所に情報を送信するために、Windows PowerShell の動詞 **Out** を使用します。 **Out-Host** コマンドレットも例外ではありません。ホスト ウィンドウ表示は、Windows PowerShell の外部で行われます。 これは、Windows PowerShell の外部にデータが送信されるときに、データが実際に削除されるので、重要です。 ホスト ウィンドウにデータをページングするパイプラインを作成しようとする場合に、これが発生します。次に示すように、リストとして書式設定を試みます。

```
PS> Get-Process | Out-Host -Paging | Format-List
```

リスト形式で処理情報のページを表示するコマンドと思ったかもしれません。 そうではなく、既定の表形式のリストが表示されます。

```
Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    101       5     1076       3316    32     0.05   2888 alg
...
    618      18    39348      51108   143   211.20    740 explorer
    257       8     9752      16828    79     3.02   2560 explorer
...
<SPACE> next page; <CR> next line; Q quit
...
```

**Out-Host** コマンドレットは、データを直接にコンソールに送信します。したがって、**Format-List** コマンドは、書式を設定するものを何も受信しません。

このコマンドを構築する正しい方法は、次に示すように、**Out-Host** コマンドレットをパイプラインの最後に置くことです。 これによって、データがページングされて表示される前に、リスト形式に書式設定するよう処理されます。

```
PS> Get-Process | Format-List | Out-Host -Paging

Id      : 2888
Handles : 101
CPU     : 0.046875
Name    : alg
...

Id      : 740
Handles : 612
CPU     : 211.703125
Name    : explorer

Id      : 2560
Handles : 257
CPU     : 3.015625
Name    : explorer
...
<SPACE> next page; <CR> next line; Q quit
...
```

これは、すべての **Out** コマンドレットに適用されます。 **Out** コマンドレットは、常にパイプラインの末尾に置く必要があります。

> [!NOTE]
> すべての **Out** コマンドレットは、コンソール ウィンドウに有効な書式設定 (行の長さの制限を含む) を使用して、テキストとして出力を表示します。

#### コンソール出力のページング (Out-Host)
既定では、Windows PowerShell は、データをホスト ウィンドウに送信します。実際にそのことを実行するのが Out-Host コマンドレットです。 Out-Host コマンドレットの主な用途は、前に説明したようにデータのページングです。 たとえば、次のコマンドは、Out-Host を使用して、Get-Command コマンドレットの出力をページングします。

```
PS> Get-Command | Out-Host -Paging
```

**more** 関数を使用して、データをページングすることも可能です。 Windows PowerShell では、**more** は、**Out-Host -Paging** を呼び出す関数です。 次のコマンドの使用例は、**more** 関数を使用して、Get-Command の出力をページングします。

```
PS> Get-Command | more
```

more 関数の引数として、1 つ以上のファイル名を含める場合、関数は指定されたファイルを読み取り、ホストにその内容をページングします。

```
PS> more c:\boot.ini
[boot loader]
timeout=5
default=multi(0)disk(0)rdisk(0)partition(1)\WINDOWS
[operating systems]
...
```

#### 出力の破棄 (Out-Null)
**Out-Null** コマンドレットは、受け取ったすべての入力を直ちに破棄するよう設計されています。 これは、コマンドを実行する副作用として受け取る不要なデータを破棄するのに便利です。 次のコマンドを入力すると、コマンドからは何も返ってきません。

```
PS> Get-Command | Out-Null
```

**Out-Null** コマンドレットは、エラー出力を破棄しません。 たとえば、次のようにコマンドを入力する場合、Windows PowerShell が 'Is-NotACommand' を認識しないことを通知するメッセージが表示されます。

```
PS> Get-Command Is-NotACommand | Out-Null
Get-Command : 'Is-NotACommand' is not recognized as a cmdlet, function, operabl
e program, or script file.
At line:1 char:12
+ Get-Command  <<<< Is-NotACommand | Out-Null
```

#### データの印刷 (Out-Printer)
**Out-Printer** コマンドレットを使用してデータを印刷できます。 **Out-Printer** コマンドレットは、プリンター名を指定しない場合、通常使うプリンターを使用します。 任意の Windows ベースのプリンターを、その表示名を指定して使用できます。 どんな種類のプリンター ポートのマッピングも (実際の物理プリンターの場合でも) 必要ありません。 たとえば、Microsoft Office 文書のイメージング ツールがインストールされている場合、次のように入力して、データをイメージ ファイルに送信できます。

```
PS> Get-Command Get-Command | Out-Printer -Name "Microsoft Office Document Image Writer"
```

#### データの保存 (Out-File)
**Out-File** コマンドレットを使用して、出力をコンソール ウィンドウにではなく、ファイルに送信することができます。 次のコマンド ラインは、プロセスの一覧をファイル **C:\temp\processlist.txt** に送信します。

```
PS> Get-Process | Out-File -FilePath C:\temp\processlist.txt
```

従来の出力のリダイレクトを使用している場合、**Out-File** コマンドレットを使用した結果は、期待どおりではない可能性があります。 その動作を理解するために、**Out-File** コマンドレット操作の状況を把握しておく必要があります。

既定では、**Out-File** コマンドレットは、Unicode ファイルを生成します。 長期的には、これが最適な既定です。しかし、ASCII ファイルを前提とするツールは、既定の出力形式では正しく動作しないことになります。 **Encoding** パラメーターを使用して、既定の出力フォーマットを ASCII 形式に変更することができます。

```
PS> Get-Process | Out-File -FilePath C:\temp\processlist.txt -Encoding ASCII
```

**Out-file** は、ファイルの内容をコンソール出力に表示されるように書式設定します。 これによって、ほとんどの状況で、コンソール ウィンドウ内に表示されるのと同じように、出力は切り捨てられます。 たとえば、次のようにコマンドを実行します。

```
PS> Get-Command | Out-File -FilePath c:\temp\output.txt
```

出力は次のようになります。

```
CommandType     Name                            Definition                     
-----------     ----                            ----------                     
Cmdlet          Add-Content                     Add-Content [-Path] <String[...
Cmdlet          Add-History                     Add-History [[-InputObject] ...
...
```

行の折り返しを画面の幅に合わせないで出力するには、**Width** パラメーターを使用して行の幅を指定します。 **Width** が 32 ビットの整数パラメーターであるため、最大値は 2147483647 です。 行の幅に、この最大値を設定するには、次のように入力します。

```
Get-Command | Out-File -FilePath c:\temp\output.txt -Width 2147483647
```

**Out-File** コマンドレットは、ちょうどコンソールに表示されるように出力を保存する場合、最も役立ちます。 出力書式設定をさらに細かく制御するには、より高度なツールが必要です。 次の章で、これらについて、またオブジェクト操作についての詳細を説明します。




<!--HONumber=Oct16_HO1-->


