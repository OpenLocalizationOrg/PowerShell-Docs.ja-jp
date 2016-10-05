---
title: "PowerShell エンジンの機能強化"
author: jasonsh
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 1b35a25312b44d14ec8771be9e17aaa43e270b61

---

# PowerShell エンジンの機能強化 #

コア PowerShell エンジンに対する以下の機能強化が PowerShell 5.1 に実装されました。


## パフォーマンス ##

いくつかの重要な部分でパフォーマンスが向上しました。

1. 起動
2. ForEach-Object や Where-Object などのコマンドレットに対するパイプライン処理が、約 50% 速くなりました 

機能強化の例をいくつか示します (結果はハードウェアによって異なる場合があります)。 

| シナリオ | 5.0 の時間 (ミリ秒) | 5.1 の時間 (ミリ秒) |
| -------- | :---------------: | :---------------: |
| `powershell -command "echo 1"` | 900 | 250 |
| 最初にこれまで PowerShell 実行します。 `powershell -command "Unknown-Command"` | 30000 | 13000 |
| コマンドの分析のキャッシュが構築: `powershell -command "Unknown-Command"` | 7000 | 520 |
| <code>1..1000000 &#124; % { }</code> | 1400 | 750 |
  
起動に関連する 1 つの変更が、一部の (サポートされていない) シナリオに影響を与える可能性があります。 PowerShell は `$pshome\*.ps1xml` ファイルを読み込まなくなりました。これらのファイルは、XML ファイルの処理でファイルと CPU にオーバーヘッドが発生しないよう、C# に変換されています。 これらのファイルは V2 のサイド バイ サイド インストールをサポートするためにまだ存在するので、ファイルの内容を変更した場合、V5 には影響がなく、影響を受けるのは V2 だけです。 これらのファイルの内容を変更するシナリオはサポートされていないことに注意してください。

もう 1 つの明らかな変更は、システムにインストールされているモジュールのために PowerShell がエクスポートしたコマンドと他の情報をキャッシュするしくみです。 これまでは、このキャッシュは `$env:LOCALAPPDATA\Microsoft\Windows\PowerShell\CommandAnalysis` ディレクトリに保存されていました。 WMF 5.1 では、キャッシュは `$env:LOCALAPPDATA\Microsoft\Windows\PowerShell\ModuleAnalysisCache` ファイル 1 つだけになっています。
詳細については、「[analysis_cache.md]()」を参照してください。

バージョン 5.1 から、PowerShell はさまざまな機能セットとプラットフォーム互換性を備える別のエディションで使用できます。



## バグ修正 ##

次の重要なバグが修正されました。

### モジュールの自動検出が完全には `$env:PSModulePath` ###

モジュールの自動検出 (コマンド呼び出し時に Import-Module を明示的に指定しないモジュールの自動的な読み込み) が、WMF3 で導入されました。 導入時、PowerShell は `$env:PSModulePath` を使用する前に `$PSHome\Modules` のコマンドを確認していました。

WMF5.1 では、この動作が `$env:PSModulePath` を完全に受け入れるように変更されています。 これにより、PowerShell によって提供されるコマンド (`Get-ChildItem` など) を定義するユーザー作成のモジュールを、自動的に読み込み、組み込みコマンドを正しくオーバーライドできます。

### ファイルのリダイレクトしない長いハードコード化 `-Encoding Unicode` ###

PowerShell のこれまでのすべてのバージョンでは、PowerShell が `-Encoding Unicode` を追加していたため、ファイル リダイレクト演算子 (`Get-ChildItem > out.txt` など) によって使用されるファイル エンコードを制御できませんでした。

WMF 5.1 からは、`$PSDefaultParameterValues` を設定することによってリダイレクトのファイル エンコードを変更できます。次に例を示します。

```
$PSDefaultParameterValues["Out-File:Encoding"] = "Ascii"
```

### メンバーにアクセスするバグ再発を修正 `System.Reflection.TypeInfo` ###

WMF5 で新しく発生したバグにより、`System.Reflection.RuntimeType` のメンバーにアクセスできませんでした (`[int].ImplementedInterfaces` など)。
WMF 5.1 ではこのバグが修正されています。


### COM オブジェクトでのいくつかの問題の修正 ###

WMF5 では、COM オブジェクト上のメソッドを呼び出して COM オブジェクトのプロパティにアクセスする新しい COM バインダーが導入されました。
この新しいバインダーによりパフォーマンスが大幅に向上しましたが、バグもいくつか含まれていました。WMF 5.1 ではそれが修正されました。

#### 引数の変換が正常に実行されないことがあった ####

次に例を示します。

```
$obj = New-Object -ComObject WScript.Shell
$obj.SendKeys([char]173)
```

SendKeys メソッドは string を受け取りますが、PowerShell は char を string に変換せず、変換を IDispatch::Invoke に延期していました。このメソッドは VariantChangeType を使用して変換を行います。この例では、結果として、期待される Volume.Mute キーではなくキー "1"、"7"、"3" が送られます。

#### 列挙可能な COM オブジェクトが正しく処理されないことがある ####

PowerShell は通常ほとんどの列挙可能なオブジェクトを列挙しますが、WMF5 で発生したバグにより、IEnumerable を実装する COM オブジェクトの列挙が行われませんでした。  たとえば、次のように入力します。

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

上の例の WMF5 は、キーと値のペアを列挙するのではなく、Scripting.Dictionary をパイプラインに誤って書き込みます。


### `[ordered]` クラスは許可されませんでした。 ###

WMF5 では、クラスで使用されるリテラル形を検証するクラスが導入されました。  `[ordered]` 型のリテラルのようになりますが、真の .NET 型ではありません。  WMF5 は、クラスの内の `[ordered]` で誤ってエラーを報告しました。

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

WMF5.1 より前では、複数のバージョンのモジュールがインストールされていて、そのすべてがヘルプ トピック (about_PSReadline など) を共有している場合、`help about_PSReadline` は複数のトピックを返し、実際のヘルプを表示する明確な方法はありませんでした。

WMF5.1 では、最新バージョンのトピックのヘルプを返すことでこれが解決されています。

Get-Help には、必要なバージョンを指定する方法がありません。これを回避するには、モジュール ディレクトリに移動し、好みのエディターなどのツールで直接ヘルプを表示します。 



<!--HONumber=Oct16_HO1-->


