---
title: "変数を使用したオブジェクトの保存"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: b1688d73-c173-491e-9ba6-6d0c1cc852de
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 6216f3e1a766c57a7549a3e3b4fbe76d043a8a41

---

# 変数を使用したオブジェクトの保存
Windows PowerShell はオブジェクトを操作します。 Windows PowerShell では変数 (本質的には名前付きのオブジェクト) を作成し、後で使用する出力を保持できます。 他のシェルで変数を使用することに慣れている場合、Windows PowerShell の変数はテキストではなくオブジェクトであることを覚えていてください。

変数は常に最初の文字が $ で指定され、名前には任意の英数字またはアンダースコアを含めることができます。

### 変数の作成
次のように有効な変数名を入力することで、変数を作成できます。

```
PS> $loc
PS>
```

**$loc** に値がないため、これは結果を返しません。 変数の作成と値の割り当てを同一の手順で行うことができます。 Windows PowerShell では、変数の作成はその変数が存在しない場合にのみ行われます。存在する場合は、指定した値が既存の変数に割り当てられます。 変数 **$loc** に現在の場所を格納するには、次のように入力します。

```
$loc = Get-Location
```

このコマンドを入力しても出力は表示されません。出力が $loc に送られるためです。 Windows PowerShell で出力が表示されるのは、他に行き先のないデータが決まって画面に送られることの副作用です。 $loc と入力することで現在の場所が表示されます。

```
PS> $loc

Path
----
C:\temp
```

**Get-Member** を使用して変数の内容に関する情報を表示できます。 $loc を Get-Member へパイプ処理すると、Get-Location からの出力と同様、これが **PathInfo** オブジェクトであることがわかります。

```
PS> $loc | Get-Member -MemberType Property

   TypeName: System.Management.Automation.PathInfo

Name         MemberType Definition
----         ---------- ----------
Drive        Property   System.Management.Automation.PSDriveInfo Drive {get;}
Path         Property   System.String Path {get;}
Provider     Property   System.Management.Automation.ProviderInfo Provider {...
ProviderPath Property   System.String ProviderPath {get;}
```

### 変数の操作
Windows PowerShell は変数を操作するいくつかのコマンドを提供します。 次のように入力すると、完全な一覧が読みやすい形式で表示されます。

```
Get-Command -Noun Variable | Format-Table -Property Name,Definition -AutoSize -Wrap
```

現在の Windows PowerShell セッションで作成する変数に加えて、いくつかのシステム定義の変数があります。 **Remove-Variable** コマンドレットを使用して、Windows PowerShell によって制御されない変数をすべてクリアできます。 すべての変数をクリアするには、次のコマンドを入力します。

```
Remove-Variable -Name * -Force -ErrorAction SilentlyContinue
```

これにより、以下に示される確認プロンプトが作成されます。

```
Confirm
Are you sure you want to perform this action?
Performing operation "Remove Variable" on Target "Name: Error".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):A
```

その後で **Get-Variable** コマンドレットを実行すると、残りの Windows PowerShell 変数が表示されます。 変数 Windows PowerShell ドライブもあるため、次のように入力してすべての Windows PowerShell 変数を表示することもできます。

```
Get-ChildItem variable:
```

### Cmd.exe 変数の使用
Windows PowerShell は、Cmd.exe ではありませんが、コマンド シェル環境で実行され、Windows のあらゆる環境で使用できるものと同じ変数を使用できます。 これらの変数は **env** という名前のドライブを介して公開されます。 これらの変数を表示するには次のように入力します。

```
Get-ChildItem env:
```

標準的な変数コマンドレットは **env:** 変数を処理するよう設計されてはいませんが、**env:** プレフィックスを指定して引き続き使用することができます。 たとえば、オペレーティング システムのルート ディレクトリを表示するために、Windows PowerShell 内から次のように入力してコマンドシェル **%SystemRoot%** 変数を使用できます。

```
PS> $env:SystemRoot
C:\WINDOWS
```

さらに、Windows PowerShell 内から環境変数を作成および変更することもできます。 Windows PowerShell からアクセスされる環境変数は、Windows の他の場所の環境変数に適用される通常の規則に準拠しています。




<!--HONumber=Oct16_HO1-->


