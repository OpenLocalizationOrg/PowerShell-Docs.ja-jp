---
title: ".NET オブジェクトと COM オブジェクトの作成 (New-Object)"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 2057b113-efeb-465e-8b44-da2f20dbf603
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4c0f405a46e16935211b3886a40c9f7d1afc7260

---

# .NET オブジェクトと COM オブジェクトを作成する (New-Object)
ソフトウェア コンポーネントの中には、さまざまなシステム管理タスクを実行できるようにする .NET Framework や COM インターフェイスを備えているものがあります。 これらのコンポーネントは Windows PowerShell から使用することもでき、コマンドレットだけではできないタスクも実行できます。 Windows PowerShell の初回リリースでは、コマンドレットの多くがリモート コンピューターに対応していません。 ここでは、イベント ログを管理する場合に、.NET Framework の **System.Diagnostics.EventLog** クラスを Windows PowerShell から直接使用して、この制限を回避する方法を紹介します。

### New-Object によるイベント ログへのアクセス
.NET Framework のクラス ライブラリには、イベント ログの管理に使用する **System.Diagnostics.EventLog** というクラスが含まれています。 .NET Framework クラスの新しいインスタンスを作成するには、**New-Object** コマンドレットと **TypeName** パラメーターを使用します。 たとえば、次のコマンドを実行すると、イベント ログの参照が作成されます。

```
PS> New-Object -TypeName System.Diagnostics.EventLog

  Max(K) Retain OverflowAction        Entries Name
  ------ ------ --------------        ------- ----
```

このコマンドによって EventLog クラスのインスタンスは作成されましたが、このインスタンスにはデータがまったく含まれていません。 これは、特定のイベント ログを指定しなかったためです。 実際のイベント ログを取得するにはどうすればよいのでしょうか。

#### New-Object でのコンストラクターの使用
特定のイベント ログを参照するには、ログの名前を指定する必要があります。 **New-Object** には、**ArgumentList** というパラメーターがあります。 このパラメーターの値として渡した引数は、オブジェクトの特殊な初期化メソッドで使用されます。 オブジェクトを構築 (construct) する目的で使用されることから、このメソッドは*コンストラクター*と呼ばれています。 たとえば、Application ログの参照を取得するには、"Application" という文字列を引数として指定します。

```
PS> New-Object -TypeName System.Diagnostics.EventLog -ArgumentList Application

Max(K) Retain OverflowAction        Entries Name
------ ------ --------------        ------- ----
16,384      7 OverwriteOlder          2,160 Application
```

> [!NOTE]
> .NET Framework の中核となるクラスの大半は System 名前空間に存在するため、指定された型名が見つからなかった場合、Windows PowerShell は、そのクラスを自動的に System 名前空間から検索しようと試みます。 したがって、「System.Diagnostics.EventLog」の部分は「Diagnostics.EventLog」と指定することもできます。

#### オブジェクトの変数への保存
オブジェクトの参照を保存し、それを現在のシェルで使用できます。 Windows PowerShell では多くの作業をパイプラインを使って実行できるため、変数を使用する機会はあまり多くありません。しかし、場合によっては、オブジェクトの参照を変数に格納した方が、オブジェクトを効率よく操作できることがあります。

Windows PowerShell では、変数 (つまり、オブジェクトに名前を付けたもの) を作成できます。 正しい Windows PowerShell コマンドであれば、その出力を変数に格納できます。 変数名の先頭には、常に $ が付きます。 Application ログの参照を変数 $AppLog に格納するには、この変数名の後に等号を入力し、続けて、Application ログ オブジェクトの作成に使用するコマンドを入力します。

```
PS> $AppLog = New-Object -TypeName System.Diagnostics.EventLog -ArgumentList Application
```

その後、「$AppLog」と入力すると、Application ログが格納されていることを確認できます。

```
PS> $AppLog

  Max(K) Retain OverflowAction        Entries Name
  ------ ------ --------------        ------- ----
  16,384      7 OverwriteOlder          2,160 Application
```

#### New-Object によるリモートのイベント ログへのアクセス
前のセクションで使用したコマンドは、アクセス先としてローカル コンピューターを想定していました。ローカル コンピューターのイベント ログを取得するのであれば、**Get-EventLog** コマンドレットを使って行うこともできます。 リモート コンピューターの Application ログにアクセスするには、引数として、ログの名前とコンピューター名 (または IP アドレス) の両方を指定する必要があります。

```
PS> $RemoteAppLog = New-Object -TypeName System.Diagnostics.EventLog Application,192.168.1.81
PS> $RemoteAppLog

  Max(K) Retain OverflowAction        Entries Name
  ------ ------ --------------        ------- ----
     512      7 OverwriteOlder            262 Application
```

これで、イベント ログの参照が $RemoteAppLog 変数に格納されました。以降、この変数を使って、具体的にどのようなタスクを実行できるのかを説明します。

#### オブジェクトのメソッドを使用したイベント ログのクリア
多くの場合、オブジェクトには、特定のタスクを実行するときに呼び出すことのできるメソッドが存在します。 **Get-Member** を使用すると、オブジェクトに関連付けられているメソッドを表示できます。 次の例は、EventLog クラスのメソッドを表示するコマンドと、その出力の抜粋です。

```
PS> $RemoteAppLog | Get-Member -MemberType Method

   TypeName: System.Diagnostics.EventLog

Name                      MemberType Definition
----                      ---------- ----------
...
Clear                     Method     System.Void Clear()
Close                     Method     System.Void Close()
...
GetType                   Method     System.Type GetType()
...
ModifyOverflowPolicy      Method     System.Void ModifyOverflowPolicy(Overfl...
RegisterDisplayName       Method     System.Void RegisterDisplayName(String ...
...
ToString                  Method     System.String ToString()
WriteEntry                Method     System.Void WriteEntry(String message),...
WriteEvent                Method     System.Void WriteEvent(EventInstance in...
```

イベント ログをクリアするには、**Clear()** メソッドを使用します。 メソッドを呼び出すときは、引数が不要な場合でも、メソッド名の後に丸かっこを必ず入力する必要があります。 Windows PowerShell は、丸かっこの有無により、それがメソッド名なのか、同名のプロパティ名なのかを判断します。 **Clear** メソッドを呼び出すには、次のように入力します。

```
PS> $RemoteAppLog.Clear()
```

ログを表示するには、次のように入力します。 イベント ログがクリアされ、エントリ数が 262 から 0 に変わっていることが確認できます。

```
PS> $RemoteAppLog

  Max(K) Retain OverflowAction        Entries Name
  ------ ------ --------------        ------- ----
     512      7 OverwriteOlder              0 Application
```

### New-Object による COM オブジェクトの作成
コンポーネント オブジェクト モデル (COM) のコンポーネントを操作するには、**New-Object** を使用します。 一口にコンポーネントと言っても、その種類は Windows Script Host (WSH) に含まれている各種ライブラリから、Internet Explorer のようなほとんどのシステムにインストールされている ActiveX アプリケーションまで多岐にわたります。

**New-Object** では、.NET Framework ランタイム呼び出し可能ラッパーを使って COM オブジェクトを作成します。したがって、COM オブジェクトを呼び出す際には .NET Framework の場合と同じ制限が適用されます。 COM オブジェクトを作成するには、使用する COM クラスのプログラム識別子 (*ProgId*) を **ComObject** パラメーターで指定する必要があります。 COM の使用上の制限や、システム上で利用できる ProgId の調査方法については、このマニュアルの範囲を超えているので詳しく説明しません。しかし、WSH などの環境に存在する、一般によく知られているようなオブジェクトについては、Windows PowerShell 内で使用できます。

WSH オブジェクトは、**WScript.Shell**、**WScript.Network**、**Scripting.Dictionary**、**Scripting.FileSystemObject** などを ProgId として指定すれば作成できます。 これらのオブジェクトを作成するコマンドの例を次に示します。

```
New-Object -ComObject WScript.Shell
New-Object -ComObject WScript.Network
New-Object -ComObject Scripting.Dictionary
New-Object -ComObject Scripting.FileSystemObject
```

これらのクラスの機能は、その多くが、Windows PowerShell から他の方法を使ってアクセスすることもできます。ただし、ショートカットの作成など、一部のタスクについては、WSH のクラスを使用した方が簡単です。

### WScript.Shell によるデスクトップ ショートカットの作成
COM オブジェクトの使用が適しているタスクの 1 つに、ショートカットの作成があります。 Windows PowerShell のホーム フォルダーに対するショートカットをデスクトップ上に作成するとします。 まず必要なことは、**WScript.Shell** の参照を作成することです。ここでは、この参照を **$WshShell** という変数に格納することにします。

```
$WshShell = New-Object -ComObject WScript.Shell
```

Get-Member は COM オブジェクトに対応しているため、次のように入力することで、オブジェクトのメンバーを調査できます。

```
PS> $WshShell | Get-Member

   TypeName: System.__ComObject#{41904400-be18-11d3-a28b-00104bd35090}

Name                     MemberType            Definition
----                     ----------            ----------
AppActivate              Method                bool AppActivate (Variant, Va...
CreateShortcut           Method                IDispatch CreateShortcut (str...
...
```

**Get-Member** には、省略可能なパラメーター **InputObject** があります。**Get-Member** に対する入力をパイプで渡す代わりに、このパラメーターを使用することもできます。 **Get-Member -InputObject $WshShell** コマンドを使用しても表示される出力結果は同じです。 **InputObject** を使用した場合、その引数は単一の項目として扱われます。 つまり、1 つの変数に複数のオブジェクトが格納されている場合、**Get-Member** では、それらはオブジェクトの配列として扱われます。 たとえば、次のように入力します。

```
PS> $a = 1,2,"three"
PS> Get-Member -InputObject $a
TypeName: System.Object[]
Name               MemberType    Definition
----               ----------    ----------
Count              AliasProperty Count = Length
...
```

**WScript.Shell CreateShortcut** メソッドは、作成するショートカット ファイルのパスのみを引数として受け取ります。 デスクトップへのフル パスを入力することもできますが、より簡単な方法があります。 通常、デスクトップは、現在のユーザーのホーム フォルダーにある "デスクトップ" というフォルダー名で表されます。 Windows PowerShell には、このフォルダーへのパスを保持する **$Home** という変数が用意されています。 この変数を使ってホーム フォルダーのパスを指定し、続けて、デスクトップ フォルダーの名前と、作成するショートカットの名前を追加します。次にその例を示します。

```
$lnk = $WshShell.CreateShortcut("$Home\Desktop\PSHome.lnk")
```

変数名と思われる情報が二重引用符内に存在した場合、Windows PowerShell は、それを対応する値に置き換えようと試みます。 一重引用符を使用した場合、変数値の置き換えは行われません。 たとえば、次のコマンドを入力してみてください。

```
PS> "$Home\Desktop\PSHome.lnk"
C:\Documents and Settings\aka\Desktop\PSHome.lnk
PS> '$Home\Desktop\PSHome.lnk'
$Home\Desktop\PSHome.lnk
```

**$lnk** という変数には、現在、新しいショートカットの参照が格納されています。 メンバーを表示するには、この変数をパイプを使って **Get-Member** に渡します。 次のように、ショートカットを完成するために必要なメンバーが出力結果として表示されます。

<pre>PS> $lnk | Get-Member TypeName: System.__ComObject#{f935dc23-1cf0-11d0-adb9-00c04fd58a0b} Name             MemberType   Definition ----             ----------   ---------- ...Save             Method       void Save ()TargetPath       Property     string TargetPath () {get} {set} ...</pre>

**TargetPath** (Windows PowerShell のアプリケーション フォルダー) を指定した後、**Save** メソッドを呼び出してショートカット **$lnk** を保存する必要があります。 Windows PowerShell のアプリケーション フォルダーのパスは変数 **$PSHome** に保存されているため、次のように入力します。

<pre>$lnk.TargetPath = $PSHome $lnk.Save()</pre>

### Windows PowerShell からの Internet Explorer の使用
Microsoft Office ファミリのアプリケーションや Internet Explorer など、多くのアプリケーションは COM を使用して自動化できます。 COM ベースのアプリケーション操作に関係する一般的なテクニックと問題点を、Internet Explorer を例に取り上げます。

Internet Explorer のインスタンスを作成するには、次のように、Internet Explorer の ProgId として **InternetExplorer.Application** を指定します。

```
$ie = New-Object -ComObject InternetExplorer.Application
```

このコマンドでは、Internet Explorer は起動しますが、表示はされません。 「Get-Process」と入力すると、iexplore という名前のプロセスが実行されていることがわかります。 実際、Windows PowerShell を終了しても、このプロセスは実行されたままです。 iexplore プロセスを終了するには、コンピューターを再起動するか、またはタスク マネージャーなどのツールを使用する必要があります。

> [!NOTE]
> 独立したプロセスとして起動する COM オブジェクトは、一般に *ActiveX 実行可能ファイル*と呼ばれ、起動時にユーザー インターフェイス ウィンドウを表示するものと、表示しないものが存在します。 Internet Explorer のように、ウィンドウは作成されても、表示状態にはしない COM オブジェクトの場合、フォーカスが Windows デスクトップに移されることが多く、ユーザーが操作できるようにするためには、ウィンドウを表示状態にする必要があります。

**$ie | Get-Member** と入力すると、Internet Explorer のプロパティおよびメソッドを表示できます。 Internet Explorer ウィンドウを表示するには、次のように、Visible プロパティを $true に設定します。

```
$ie.Visible = $true
```

次に、Navigate メソッドを使用すると、特定の Web アドレスに移動できます。

```
$ie.Navigate("http://www.microsoft.com/technet/scriptcenter/default.mspx")
```

Internet Explorer オブジェクト モデルの他のメンバーを使用すると、この Web ページからテキストの内容を取得できます。 次のコマンドを実行すると、現在の Web ページの本文に含まれる HTML テキストが表示されます。

```
$ie.Document.Body.InnerText
```

Internet Explorer を PowerShell 内から終了するには、Quit() メソッドを呼び出します。

```
$ie.Quit()
```

この場合、アプリケーションは強制的に終了されます。 一見すると、まだ COM オブジェクトが有効であるかのように見えますが、この時点で、$ie 変数には正しい参照が存在しません。 使用を試みると、オートメーション エラーが発生します。

```
PS> $ie | Get-Member
Get-Member : Exception retrieving the string representation for property "Appli
cation" : "The object invoked has disconnected from its clients. (Exception fro
m HRESULT: 0x80010108 (RPC_E_DISCONNECTED))"
At line:1 char:16
+ $ie | Get-Member <<<<
```

コマンド "$ie = $null" などのような形で残っている参照を削除することも、次のように入力して変数を完全に削除することもできます。

```
Remove-Variable ie
```

> [!NOTE]
> 参照を削除したときに、ActiveX 実行可能ファイルを終了するか、実行を継続するかに関して、共通の標準は存在しません。 アプリケーションが終了するかどうかは、アプリケーションが可視状態であるか、編集中のドキュメントが存在するか、Windows PowerShell がまだ実行されているかなど、さまざまな条件に依存します。 そのため、Windows PowerShell で使用する ActiveX 実行可能ファイルごとに、終了時の動作をテストしておく必要があります。

### .NET Framework によってラップされた COM オブジェクトの警告の取得
COM オブジェクトに、.NET Framework *ランタイム呼び出し可能ラッパー* (RCW) が関連付けられていて、これが **New-Object** で使用されることがあります。 RCW の動作は通常の COM オブジェクトの動作とは異なる場合があるため、**New-Object** には、RCW アクセスに関する警告を取得する **Strict** パラメーターが用意されています。 **Strict** パラメーターを指定し、RCW を使用する COM オブジェクトを作成した場合、次のような警告メッセージが表示されます。

```
PS> $xl = New-Object -ComObject Excel.Application -Strict
New-Object : The object written to the pipeline is an instance of the type "Mic
rosoft.Office.Interop.Excel.ApplicationClass" from the component's primary inte
rop assembly. If this type exposes different members than the IDispatch members
, scripts written to work with this object might not work if the primary intero
p assembly is not installed.
At line:1 char:17
+ $xl = New-Object  <<<< -ComObject Excel.Application -Strict
```

オブジェクトは作成されますが、標準の COM オブジェクトではないことを示す警告が表示されます。




<!--HONumber=Oct16_HO1-->


