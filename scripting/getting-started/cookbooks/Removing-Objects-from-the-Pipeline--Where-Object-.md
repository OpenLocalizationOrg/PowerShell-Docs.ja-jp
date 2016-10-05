---
title: "パイプラインからオブジェクトを削除する (Where-Object)"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 01df8b22-2d22-4e2c-a18d-c004cd3cc284
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 94117fcf337ecf550d6df1d167e608ba64582c03

---

# パイプラインからオブジェクトを削除する (Where-Object)
Windows PowerShell では、考えていた数よりも多くのオブジェクトが生成され、パイプラインに渡されることがよくあります。 **Format** コマンドレットを使用して、特定のオブジェクトのプロパティを指定し、表示することができます。しかし、これはオブジェクト全体を表示から削除するという問題には役立ちません。 パイプラインの終了前に、オブジェクトをフィルタリングすることによって、最初に生成されたオブジェクトのサブセット上でのみアクションを実行できます。

Windows PowerShell には、**Where-Object** コマンドレットがあります。それを使用すると、パイプラインの各オブジェクトをテストし、特定のテスト条件を満たしている場合にのみ、オブジェクトをパイプラインに沿って渡すことが可能になります。 テストを通過しなかったオブジェクトは、パイプラインから削除されます。 テスト条件は、**Where-ObjectFilterScript** パラメーターの値として指定します。

### Where-Object を使用して単純なテストを実行する
**FilterScript** の値は、true または false に評価される*スクリプト ブロック* - (中かっこ {} で囲まれた Windows PowerShell コマンドの 1 つ以上のコマンド) です。 これらのスクリプト ブロックはごく単純にすることができますが、作成するには別の Windows PowerShell の概念である比較演算子について知っておく必要があります。 比較演算子は、演算子の両辺のアイテムを比較します。 比較演算子は、'-' 文字で始まり、名前が続きます。 基本的な比較演算子は、ほとんどの種類のオブジェクトで機能します。 より高度な比較演算子の中には、テキストまたは配列でのみ機能するものもあります。

> [!NOTE]
> 既定では、テキストで使用する場合、Windows PowerShell の比較演算子は大文字小文字を区別しません。

解析の考慮事項のため、<、>、= などのシンボルは、比較演算子として使用されません。 代わりに、比較演算子は文字で構成されます。 基本的な比較演算子を次の表に挙げます。

|比較演算子|意味|例 (true を返す)|
|-----------------------|-----------|--------------------------|
|-eq|次の値と等しい|1 -eq 1|
|-ne|次の値と等しくない|1 -ne 2|
|-lt|次の値未満|1 -lt 2|
|-le|次の値以下|1 -le 2|
|-gt|次の値より大きい|2 -gt 1|
|-ge|次の値以上|2 -ge 1|
|-like|次の文字列と類似 (テキストのワイルドカード比較)|"file.doc"のように"f\ * 元ですか?"|
|-notlike|次の文字列と類似していない (テキストのワイルドカード比較)|"file.doc"の notlike"p\ * .doc"|
|-contains|［内容］|1,2,3 -contains 1|
|-notcontains|［次の値を含まない］|1,2,3 -notcontains 4|

Where-Object スクリプト ブロックは、パイプライン中の現在のオブジェクトを参照するために、特殊変数 '$_' を使用します。 次に挙げるのは、その働きを示す例です。 数値の一覧があり、3 未満の値だけを返したい場合、Where-Object を使用して、次のように入力して数値を抽出できます。

```
PS> 1,2,3,4 | Where-Object -FilterScript {$_ -lt 3}
1
2
```

### オブジェクトのプロパティに基づくフィルタリング
$_ は、現在のパイプライン オブジェクトを参照するので、テストのためにそのプロパティにアクセスできます。

例として、WMI の Win32_SystemDriver クラスを取り上げます。 特定のシステムには、何百ものシステム ドライバーが存在する可能性がありますが、関心を向けるのは、現在実行中のシステム ドライバーなど、システム ドライバーの特定のセットだけの場合があります。 Get-Member を使用して、Win32_SystemDriver メンバーを表示する場合 (**Get-WmiObject -Class Win32_SystemDriver | Get-Member -MemberType Property**)、関連するプロパティは State に、ドライバーが実行中であればその値は "Running" になります。 システム ドライバーをフィルタリングして、次のように入力して実行中のドライバーのみを選択できます。

```
Get-WmiObject -Class Win32_SystemDriver | Where-Object -FilterScript {$_.State -eq "Running"}
```

生成されるのは、依然として長い一覧です。 StartMode 値を同様にテストして、自動的に起動されるドライバーのセットのみを選択するようフィルタリングすることにします。

```
PS> Get-WmiObject -Class Win32_SystemDriver | Where-Object -FilterScript {$_.State -eq "Running"} | Where-Object -FilterScript {$_.StartMode -eq "Auto"}

DisplayName : RAS Asynchronous Media Driver
Name        : AsyncMac
State       : Running
Status      : OK
Started     : True

DisplayName : Audio Stub Driver
Name        : audstub
State       : Running
Status      : OK
Started     : True
```

これにより、もう必要のない数多くの情報が与えられます。それらのドライバーが実行中なのはわかっています。 事実、この時点でおそらく必要とされる情報は、名前と表示名だけです。 次のコマンドには、それら 2 つのプロパティのみが含まれており、よりシンプルに結果を出力します。

```
PS> Get-WmiObject -Class Win32_SystemDriver | Where-Object -FilterScript {$_.State -eq "Running"} | Where-Object -FilterScript {$_.StartMode -eq "Manual"} | Format-Table -Property Name,DisplayName

Name                                    DisplayName
----                                    -----------
AsyncMac                                RAS Asynchronous Media Driver
Fdc                                     Floppy Disk Controller Driver
Flpydisk                                Floppy Disk Driver
Gpc                                     Generic Packet Classifier
IpNat                                   IP Network Address Translator
mouhid                                  Mouse HID Driver
MRxDAV                                  WebDav Client Redirector
mssmbios                                Microsoft System Management BIOS Driver
```

上記のコマンドには Where-Object 要素が 2 つありますが、次のように -and 論理演算子を使用して、Where-Object 要素 1 つで表現することができます。

```
Get-WmiObject -Class Win32_SystemDriver | Where-Object -FilterScript { ($_.State -eq "Running") -and ($_.StartMode -eq "Manual") } | Format-Table -Property Name,DisplayName
```

標準的な論理演算子を次の表に挙げます。

|論理演算子|意味|例 (true を返す)|
|--------------------|-----------|--------------------------|
|-and|論理積。両辺が true の場合は true|(1 -eq 1) -and (2 -eq 2)|
|-or|論理和。どちらかの辺が true の場合は true|(1 -eq 1) -or (1 -eq 2)|
|-not|論理否定。true と false を逆にする|-not (1 -eq 2)|
|!|論理否定。true と false を逆にする|\!(1-eq 2)|




<!--HONumber=Oct16_HO1-->


