---
title: "WMF 5.1 (プレビュー) のバグ修正"
ms.date: 2016-07-13
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: keithb
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 8a7774b36f15ff790c31d4c1a8bc69be257b8508

---

# WMF 5.1 (プレビュー) のバグ修正#

## バグ修正 ##

WMF 5.1 では、次の重要なバグが修正されました。

### モジュールの自動検出が完全には `$env:PSModulePath` ###

モジュールの自動検出 (コマンド呼び出し時に Import-Module を明示的に指定しないモジュールの自動的な読み込み) が、WMF 3 で導入されました。 導入時、PowerShell は `$env:PSModulePath` を使用する前に `$PSHome\Modules` のコマンドを確認していました。

WMF 5.1 では、この動作が `$env:PSModulePath` を完全に受け入れるように変更されています。 これにより、PowerShell によって提供されるコマンド (`Get-ChildItem` など) を定義するユーザー作成のモジュールを、自動的に読み込み、組み込みコマンドを正しくオーバーライドできます。

### ファイルのリダイレクトしない長いハードコード化 `-Encoding Unicode` ###

PowerShell のこれまでのすべてのバージョンでは、PowerShell が `-Encoding Unicode` を追加していたため、ファイル リダイレクト演算子 (`Get-ChildItem > out.txt` など) によって使用されるファイル エンコードを制御できませんでした。

WMF 5.1 からは、`$PSDefaultParameterValues` を設定することによってリダイレクトのファイル エンコードを変更できます。

```
$PSDefaultParameterValues["Out-File:Encoding"] = "Ascii"
```

### メンバーにアクセスするバグ再発を修正 `System.Reflection.TypeInfo` ###

WMF 5.0 で新しく発生したバグにより、`System.Reflection.RuntimeType` のメンバーにアクセスできませんでした (`[int].ImplementedInterfaces` など)。
WMF 5.1 ではこのバグが修正されています。


### COM オブジェクトでのいくつかの問題の修正 ###

WMF 5.0 では、COM オブジェクト上のメソッドを呼び出して COM オブジェクトのプロパティにアクセスする新しい COM バインダーが導入されました。 この新しいバインダーによりパフォーマンスが大幅に向上しましたが、バグもいくつか含まれていました。WMF 5.1 ではそれが修正されました。

#### 引数の変換が正常に実行されないことがあった ####

次に例を示します。

```
$obj = New-Object -ComObject WScript.Shell
$obj.SendKeys([char]173)
```

SendKeys メソッドは string を受け取りますが、PowerShell は char を string に変換せず、変換を IDispatch::Invoke に延期していました。このメソッドは VariantChangeType を使用して変換を行います。この例では、結果として、期待される Volume.Mute キーではなくキー "1"、"7"、"3" が送られます。

#### 列挙可能な COM オブジェクトが正しく処理されないことがある ####

PowerShell は通常ほとんどの列挙可能なオブジェクトを列挙しますが、WMF 5.0 で発生したバグにより、IEnumerable を実装する COM オブジェクトの列挙が行われませんでした。  たとえば、次のように入力します。

```
function Get-COMDictionary
{
    $d = New-Object -ComObject Scripting.Dictionary
    $d.Add('a', 2)
    $d.Add('b', 2)
    return $d
}

$x = Get-COMDictionary
```

上の例では、WMF 5.0 が、キーと値のペアを列挙せずに、誤って Scripting.Dictionary をパイプラインに書き込みます。

この変更は、[Connect の問題 1752224](https://connect.microsoft.com/PowerShell/feedback/details/1752224) も解消します。

### `[ordered]` クラスは許可されませんでした。 ###

WMF 5.0 では、クラスで使用されるリテラル形を検証するクラスが導入されました。  
`[ordered]` 型のリテラルのようになりますが、真の .NET 型ではありません。 WMF 5.0 は、クラスの内の `[ordered]` で誤ってエラーを報告しました。

```
class CThing
{
    [object] foo($i)
    {
        [ordered]@{ Thing = $i }
    }
}
```


### 複数のバージョンがあるトピックについてのヘルプが機能しない ###

WMF 5.1 より前では、複数のバージョンのモジュールがインストールされていて、そのすべてがヘルプ トピック (about_PSReadline など) を共有している場合、`help about_PSReadline` は複数のトピックを返し、実際のヘルプを表示する明確な方法はありませんでした。

WMF 5.1 では、最新バージョンのトピックのヘルプを返すことでこれが解決されています。

`Get-Help` ヘルプを表示するバージョンを指定する方法を提供しません。 これを回避するには、モジュール ディレクトリに移動し、好みのエディターなどのツールで直接ヘルプを表示します。 



<!--HONumber=Oct16_HO1-->


