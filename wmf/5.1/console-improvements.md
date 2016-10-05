---
title: "WMF 5.1 のコンソール機能強化 (プレビュー)"
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
ms.openlocfilehash: 2a17fdd4092adf734398f38bec915d53c1b3e566

---

# WMF 5.1 のコンソール機能強化 (プレビュー)#

## PowerShell コンソールの機能強化

コンソールのエクスペリエンスを改善するため、WMF 5.1 の powershell.exe が次のように変更されました。

###VT100 のサポート

Windows 10 では、[VT100 エスケープ シーケンス](https://msdn.microsoft.com/en-us/library/windows/desktop/mt638032(v=vs.85).aspx)のサポートが追加されました。
PowerShell は、テーブルの幅を計算するとき、特定の VT100 書式指定エスケープ シーケンスを無視します。

また、PowerShell に追加された新しい API を書式指定コードで使用すると、VT100 がサポートされているかどうかを判定できます。 たとえば、次のように入力します。

```
if ($host.UI.SupportsVirtualTerminal)
{
    $esc = [char]0x1b
    "A yellow ${esc}[93mhello${esc}[0m"
}
else
{
    "A default hello"
}
```
これは、Select-String からの一致を強調表示するために使用できる完全な[例](https://gist.github.com/lzybkr/dcb973dccd54900b67783c48083c28f7)です。
例を `MatchInfo.format.ps1xml` という名前のファイルに保存した後、使用するには、プロファイルまたはその他の場所で、`Update-FormatData -Prepend MatchInfo.format.ps1xml` を実行します。

VT100 エスケープ シーケンスは、Windows 10 Anniversary 更新以降でのみサポートされることに注意してください。それより前のシステムではサポートされません。   

### PSReadline での vi モードのサポート

[PSReadline](https://github.com/lzybkr/PSReadLine) は、vi モードのサポートを追加します。 vi モードを使用するには、`Set-PSReadline -EditMode vi` を実行します。

### 対話型の入力を使用する stdin のリダイレクト 

以前のバージョンでは、stdin がリダイレクトされ、コマンドを対話的に入力するときは、PowerShell を `powershell -File -` で起動する必要がありました。

WMF 5.1 では、この見つけにくいオプションは不要になりました。 `powershell` のようなオプションを何も使わずに PowerShell を起動できます。

PSReadline は現在はリダイレクトされた stdin をサポートせず、リダイレクトされた stdin での組み込みコマンド ライン編集エクスペリエンスは非常に限られたものであることに注意してください (たとえば、方向キーは機能しません)。 PSReadline の将来のリリースではこの問題が対処されるはずです。   



<!--HONumber=Oct16_HO1-->


