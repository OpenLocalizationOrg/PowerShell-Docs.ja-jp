---
title: "コンソールの操作性の改善"
author: jasonsh
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e6653a02421e3aec3910a70c64f7cf7cecd696ab

---

>注: わかりやすいタイトルと簡単な説明を提供します

## PowerShell コンソールの改善

コンソールの操作性を改善するために、Powershell.exe が次のように変更されました。

1. VT100 のサポート

Windows 10 では、[VT100 エスケープ シーケンス](https://msdn.microsoft.com/en-us/library/windows/desktop/mt638032(v=vs.85).aspx)のサポートが追加されました。
PowerShell は、テーブルの幅を計算するとき、特定の VT100 書式指定エスケープ シーケンスを無視します。

また、PowerShell に追加された新しい API を書式指定コードで使用すると、VT100 がサポートされているかどうかを判定できます。  たとえば、次のように入力します。

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

VT100 エスケープ シーケンスは、Windows 10 Anniversary 更新プログラム以降が適用された Windows 10 でのみサポートされる点に注意してください。それより前のシステムではサポートされません。   

2. PSReadline での vi モードのサポート

[PSReadline](https://github.com/lzybkr/PSReadLine) は、vi モードのサポートを追加します。 vi モードを使用するには、`Set-PSReadline -EditMode vi` を実行します。

3. 対話型の入力を使用する stdin のリダイレクト 

以前のバージョンでは、stdin がリダイレクトされ、コマンドを対話的に入力するときは、PowerShell を `powershell -File -` で起動する必要がありました。

WMF 5.1 では、この見つけにくいオプションは不要になり、`powershell` といったオプションを何も使わずに PowerShell を起動できます。

PSReadline は現在はリダイレクトされた stdin をサポートせず、リダイレクトされた stdin での組み込みコマンド ライン編集エクスペリエンスは非常に限られたものであることに注意してください (たとえば、方向キーは機能しません)。  PSReadline の将来のリリースではこの問題が対処されるはずです。   


<!--HONumber=Oct16_HO1-->


