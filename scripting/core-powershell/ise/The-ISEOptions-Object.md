---
title: "ISEOptions オブジェクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 75e2a76f-f3d1-490b-ad5d-e3829946aabb
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 1aee849dd8b89492a641560ed4ef163c3cb96da4

---

# ISEOptions オブジェクト
  **ISEOptions** オブジェクトは、Windows PowerShell ISE のさまざまな設定を表します。 これは **Microsoft.PowerShell.Host.ISE.ISEOptions** クラスのインスタンスです。

 **ISEOptions** オブジェクトは、次のメソッドとプロパティを提供します。

 **メソッド**

-   [RestoreDefaultConsoleTokenColors()](#rdctc)

-   [RestoreDefaults()](#rd)

-   [RestoreDefaultTokenColors()](#rdtc)

-   [RestoreDefaultXmlTokenColors()](#rdxtc)

 **プロパティ**

-   [AutoSaveMinuteInterval](#asmi)

-   [CommandPaneBackgroundColor](#cpbc)

-   [CommandPaneUp](#cpu)

-   [ConsolePaneBackgroundColor](#conpbc)

-   [ConsolePaneForegroundColor](#conpfc)

-   [ConsolePaneTextBackgroundColor](#conptbc)

-   [ConsoleTokenColors](#contc)

-   [DebugBackgroundColor](#dbc)

-   [DebugForegroundColor](#dfc)

-   [DefaultOptions](#do)

-   [ErrorBackgroundColor](#ebc)

-   [ErrorForegroundColor](#efc)

-   [FontName](#fn)

-   [フォント サイズ](#fs)

-   [IntellisenseTimeoutInSeconds](#itis)

-   [MruCount](#mc)

-   [OutputPaneBackgroundColor](#opbc)

-   [OutputPaneTextForegroundColor](#optfc)

-   [OutputPaneTextBackgroundColor](#optbc)

-   [ScriptPaneBackgroundColor](#spbc)

-   [ScriptPaneForegroundColor](#spfc)

-   [SelectedScriptPaneState](#ssps)

-   [ShowDefaultSnippets](#sds)

-   [ShowIntellisenseInConsolePane](#siicp)

-   [ShowIntellisenseInScriptPane](#siisp)

-   [ShowLineNumbers](#sln)

-   [ShowOutlining](#so)

-   [ShowToolBar](#stb)

-   [ShowWarningBeforeSavingOnRun](#swbsor)

-   [ShowWarningForDuplicateFiles](#swfdf)

-   [TokenColors](#tc)

-   [UseEnterToSelectInConsolePaneIntellisense](#uetsicpi)

-   [UseEnterToSelectInScriptPaneIntellisense](#uetsispi)

-   [UseLocalHelp](#ulh)

-   [VerboseBackgroundColor](#vbc)

-   [VerboseForegroundColor](#vfc)

-   [WarningBackgroundColor](#wbc)

-   [WarningForegroundColor](#wfc)

-   [XmlTokenColors](#xtc)

-   [ズーム](#z)

## メソッド

###  <a name="rdctc"></a> RestoreDefaultConsoleTokenColors\(\)
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 コンソール ウィンドウのトークンの色の既定値を復元します。

```
# Changes the color of the commands in the Console pane to red and then restores it to its default value.
$psISE.Options.ConsoleTokenColors["Command"] = "red"
$psISE.Options.RestoreDefaultConsoleTokenColors()
```

###  <a name="rd"></a> RestoreDefaults\(\)
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウのすべてのオプション設定の既定値を復元します。 メッセージが再度表示されることを防ぐため、標準のチェック ボックスを提供する各種の警告メッセージの動作もリセットされます。

```
# Changes the background color in the Console pane and then restores it to its default value.
$psISE.Options.ConsolePaneBackgroundColor = "orange"
$psISE.Options.RestoreDefaults()
```

###  <a name="rdtc"></a> RestoreDefaultTokenColors\(\)
  Windows PowerShell ISE 2.0 以降でサポートされています。

 スクリプト ウィンドウのトークンの色の既定値を復元します。

```
# Changes the color of the comments in the Script pane to red and then restores it to its default value.
$psISE.Options.TokenColors["Comment"]="red"
$psISE.Options.RestoreDefaultTokenColors()
```

###  <a name="rdxtc"></a> RestoreDefaultXmlTokenColors\(\)
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Windows PowerShell ISE で表示される XML 要素のトークンの色の既定値を復元します。 [XmlTokenColors](#xtc) も参照してください。

```
# Changes the color of the comments in XML data to red and then restores it to its default value.
$psISE.Options.XmlTokenColors["Comment"]="red"
$psISE.Options.RestoreDefaultXmlTokenColors()
```

## プロパティ

###  <a name="asmi"></a> AutoSaveMinuteInterval
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Windows PowerShell ISE によるファイルの自動保存の処理間隔を分単位で指定します。 既定値は 2 分です。 値は整数です。

```
# Changes the number of minutes between automatic save operations to every 3 minutes.
$psISE.Options.AutoSaveMinuteInterval = 3
```

###  <a name="cpbc"></a> CommandPaneBackgroundColor
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。  後のバージョンについては、[ConsolePaneBackgroundColor](#conpbc) を参照してください。

 コマンド ウィンドウの背景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the background color of the Command pane to orange.
$psISE.Options.CommandPaneBackgroundColor = "orange"
```

###  <a name="cpu"></a> CommandPaneUp
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。

 コマンド ウィンドウを出力ウィンドウの上に配置するかどうかを指定します。

```
# Moves the Command pane to the top of the screen.
$psISE.Options.CommandPaneUp  = $true

```

###  <a name="conpbc"></a> ConsolePaneBackgroundColor
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 コンソール ウィンドウの背景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the background color of the Console pane to red.
$psISE.Options.ConsolePaneBackgroundColor = "red"
```

###  <a name="conpfc"></a> ConsolePaneForegroundColor
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 コンソール ウィンドウのテキストの前景色を指定します。

```
# Changes the foreground color of the text in the Console pane to yellow.
$psISE.Options.ConsolePaneForegroundColor  = "yellow"

```

###  <a name="conptbc"></a> ConsolePaneTextBackgroundColor
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 コンソール ウィンドウのテキストの背景色を指定します。

```
# Changes the background color of the Console pane text to pink.
$psISE.Options.ConsolePaneTextBackgroundColor = "pink"
```

###  <a name="contc"></a> ConsoleTokenColors
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Windows PowerShell ISE のコンソール ウィンドウの IntelliSense のトークンの色を指定します。 このプロパティは、コンソール ウィンドウのトークンの種類と色の名前/値のペアを含むディクショナリ オブジェクトです。 スクリプト ウィンドウで IntelliSense トークンの色を変更する場合は、[TokenColors](#tc) を参照してください。 色を既定値にリセットする場合、[RestoreDefaultConsoleTokenColors()](#rdctc) を参照してください。 次のトークンの色を設定することができます: Attribute、Command、CommandArgument、CommandParameter、Comment、GroupEnd、GroupStart、Keyword、LineContinuation、LoopLabel、Member、NewLine、Number、Operator、Position、StatementSeparator、String、Type、Unknown、Variable。

```
# Sets the color of commands to green.
$psISE.Options.ConsoleTokenColors["Command"] = "green"
# Sets the color of keywords to magenta.
$psISE.Options.ConsoleTokenColors["Keyword"] = "magenta"

```

###  <a name="dbc"></a> DebugBackgroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示されるデバッグ テキストの背景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the background color for the debug text that appears in the Console pane to blue.
$psISE.Options.DebugBackgroundColor ='#0000FF'
```

###  <a name="dfc"></a> DebugForegroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示されるデバッグ テキストの前景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the foreground color for the debug text that appears in the Console pane to yellow.
$psISE.Options.DebugForegroundColor ="yellow"
```

###  <a name="do"></a> DefaultOptions
  Windows PowerShell ISE 2.0 以降でサポートされています。

 Reset メソッドを使用する場合に使用する既定値を指定するプロパティのコレクション。

```
# Displays the name of the default options. This example is from ISE 4.0.
$psISE.Options.DefaultOptions

SelectedScriptPaneState                   : Top
ShowDefaultSnippets                       : True
ShowToolBar                               : True
ShowOutlining                             : True
ShowLineNumbers                           : True
TokenColors                               : {[Attribute, #FF00BFFF], [Command, #FF0000FF], [CommandArgument, #FF8A2BE2], [CommandParameter, #FF000080]...}
ConsoleTokenColors                        : {[Attribute, #FFB0C4DE], [Command, #FFE0FFFF], [CommandArgument, #FFEE82EE], [CommandParameter, #FFFFE4B5]...}
XmlTokenColors                            : {[Comment, #FF006400], [CommentDelimiter, #FF008000], [ElementName, #FF8B0000], [MarkupExtension, #FFFF8C00]...}
DefaultOptions                            : Microsoft.PowerShell.Host.ISE.ISEOptions
FontSize                                  : 9
Zoom                                      : 100
FontName                                  : Lucida Console
ErrorForegroundColor                      : #FFFF0000
ErrorBackgroundColor                      : #00FFFFFF
WarningForegroundColor                    : #FFFF8C00
WarningBackgroundColor                    : #00FFFFFF
VerboseForegroundColor                    : #FF00FFFF
VerboseBackgroundColor                    : #00FFFFFF
DebugForegroundColor                      : #FF00FFFF
DebugBackgroundColor                      : #00FFFFFF
ConsolePaneBackgroundColor                : #FF012456
ConsolePaneTextBackgroundColor            : #FF012456
ConsolePaneForegroundColor                : #FFF5F5F5
ScriptPaneBackgroundColor                 : #FFFFFFFF
ScriptPaneForegroundColor                 : #FF000000
ShowWarningForDuplicateFiles              : True
ShowWarningBeforeSavingOnRun              : True
UseLocalHelp                              : True
AutoSaveMinuteInterval                    : 2
MruCount                                  : 10
ShowIntellisenseInConsolePane             : True
ShowIntellisenseInScriptPane              : True
UseEnterToSelectInConsolePaneIntellisense : True
UseEnterToSelectInScriptPaneIntellisense  : True
IntellisenseTimeoutInSeconds              : 3

```

###  <a name="ebc"></a> ErrorBackgroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示されるエラー テキストの背景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the background color for the error text that appears in the Console pane to black.
$psISE.Options.ErrorBackgroundColor="black"
```

###  <a name="efc"></a> ErrorForegroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示されるエラー テキストの前景色を指定します。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the foreground color for the error text that appears in the console pane to green.
$psISE.Options.ErrorForegroundColor ="green"
```

###  <a name="fn"></a> FontName
  Windows PowerShell ISE 2.0 以降でサポートされています。

 スクリプト ウィンドウおよびコンソール ウィンドウの両方で現在使用しているフォント名を指定します。

```
# Changes the font used in both panes.
$psISE.Options.FontName = "courier new"
```

###  <a name="fs"></a> フォント サイズ
  Windows PowerShell ISE 2.0 以降でサポートされています。

 整数値としてフォント サイズを指定します。 スクリプト ウィンドウ、コマンド ウィンドウ、出力ウィンドウで使用されます。 有効な値の範囲は、8 ~ 32 です。

```
# Changes the font size in all panes.
$psISE.Options.FontSize = 20

```

###  <a name="itis"></a> IntellisenseTimeoutInSeconds
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 IntelliSense が、現在入力しているテキストを解決しようとするために使用する秒数を指定します。 この秒数が経ったら、IntelliSense はタイムアウトし、入力を続けることができます。 既定値は 3 秒です。 値は整数です。

```
# Changes the number of seconds for IntelliSense syntax recognition to 5.
$psISE.Options.IntellisenseTimeoutInSeconds = 5
```

###  <a name="mc"></a> MruCount
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Windows PowerShell ISE が追跡し、**[ファイルを開く]** メニューの下部で表示する、最近使ったファイルの数を指定します。 既定値は 10 です。 値は整数です。

```
# Changes the number of recently used files that appear at the bottom of the File Open menu to 5.
$psISE.Options.MruCount = 5
```

###  <a name="opbc"></a> OutputPaneBackgroundColor
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。  後のバージョンについては、[ConsolePaneBackgroundColor](#conpbc) を参照してください。

 出力ウィンドウ自体の背景色を取得または設定する読み取り/書き込みのプロパティ。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```
# Changes the background color of the Output pane to gold.
$psISE.Options.OutputPaneForegroundColor = "gold"

```

###  <a name="optfc"></a> OutputPaneTextForegroundColor
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。  後のバージョンについては、[ConsolePaneForegroundColor](#conpfc) を参照してください。

 Windows PowerShell ISE 2.0 で出力ウィンドウのテキストの前景色を変更する読み取り/書き込みプロパティ。

```
# Changes the foreground color of the text in the Output Pane to blue.
$psISE.Options.OutputPaneTextForegroundColor  = "blue"

```

###  <a name="optbc"></a> OutputPaneTextBackgroundColor
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。  後のバージョンについては、[ConsolePaneTextBackgroundColor](#conptbc) を参照してください。

 出力ウィンドウのテキストの背景色を変更する読み取り/書き込みプロパティ。

```
# Changes the background color of the Output pane text to pink.
$psISE.Options.OutputPaneTextBackgroundColor = "pink"
```

###  <a name="spbc"></a> ScriptPaneBackgroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 ファイルの背景色を取得または設定する読み取り/書き込みのプロパティ。 これは **System.Windows.Media.Color** クラスのインスタンスです。

```

# Sets the color of the script pane background to yellow.
$psISE.Options.ScriptPaneBackgroundColor = ”yellow”

```

###  <a name="spfc"></a> ScriptPaneForegroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 スクリプト ウィンドウのスクリプト以外のファイルの前景色を取得または設定する読み取り/書き込みのプロパティ。
スクリプト ファイルの前景色を設定する場合、[TokenColors](The-ISEOptions-Object.md#tc) プロパティを使用してください。

```
# Sets the foreground to color of non-script files in the script pane to green.
$psISE.Options.ScriptPaneBackgroundColor = "green"

```

###  <a name="ssps"></a> SelectedScriptPaneState
  Windows PowerShell ISE 2.0 以降でサポートされています。

 ディスクプレイでのスクリプト ウィンドウの位置を取得または設定する読み取り/書き込みのプロパティ。 「Maximized」、「Top」、「Right」のいずれかの文字列を指定します。

```
# Moves the Script Pane to the top.
$psISE.Options.SelectedScriptPaneState = "Top"
# Moves the Script Pane to the right.
$psISE.Options.SelectedScriptPaneState = "Right"
# Maximizes the Script Pane
$psISE.Options.SelectedScriptPaneState = "Maximized"

```

###  <a name="sds"></a> ShowDefaultSnippets
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 スニペットの **CTRL + J** 一覧に、Windows PowerShell に含まれるスターター セットを含めるどうかを指定します。 **$false** に設定すると、ユーザー定義のスニペットのみが **CTRL + J** 一覧に表示されます。 既定値は **$true** です。

```
# Hide the default snippets from the CTRL+J list.
$psISe.Options.ShowDefaultSnippets = $false
```

###  <a name="siicp"></a> ShowIntellisenseInConsolePane
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 IntelliSense がコンソール ウィンドウで構文、パラメーター、および値の候補を提供するかどうかを指定します。 既定値は **$true** です。

```
# Turn off IntelliSense in the console pane.
$psISe.Options.ShowIntellisenseInConsolePane = $false
```

###  <a name="siisp"></a> ShowIntellisenseInScriptPane
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 IntelliSense がスクリプト ウィンドウで構文、パラメーター、および値の候補を提供するかどうかを指定します。 既定値は **$true** です。

```
# Turn off IntelliSense in the Script pane.
$psISe.Options.ShowIntellisenseInScriptPane = $false
```

###  <a name="sln"></a> ShowLineNumbers
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 スクリプト ペインの左の余白に行番号を表示するかどうかを指定します。 既定値は **$true** です。

```
# Turn off line numbers in the Script pane.
$psISe.Options.ShowLineNumbers = $false
```

###  <a name="so"></a> ShowOutlining
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 スクリプト ペインの左の余白のコードのセクションの横にある展開と折りたたみの角かっこを表示するかどうかを指定します。 マイナス記号をクリックすることができますが表示される場合を折りたたんだり展開したり、テキストのブロックを展開するプラス \(+\) アイコンをクリックするテキストのブロックの横にある \(-\) アイコン。 既定値は **$true** です。

```
# Turn off outlining in the Script pane.
$psISe.Options.ShowOutlining = $false
```

###  <a name="stb"></a> ShowToolBar
  Windows PowerShell ISE 2.0 以降でサポートされています。

 Windows PowerShell ISE ウィンドウの上部に ISE ツールバーを表示するかどうかを指定します。 既定値は **$true** です。

```
# Show the toolbar.
$psISe.Options.ShowToolBar = $true
```

###  <a name="swbsor"></a> ShowWarningBeforeSavingOnRun
  Windows PowerShell ISE 2.0 以降でサポートされています。

 スクリプトが実行される前に自動的に保存されるときに警告メッセージを表示するかどうかを指定します。 既定値は **$true** です。

```
# Enable the warning message when an attempt
# is made to run a script without saving it first.
$psISE.Options.ShowWarningBeforeSavingOnRun=$true

```

###  <a name="swfdf"></a> ShowWarningForDuplicateFiles
  Windows PowerShell ISE 2.0 以降でサポートされています。

 さまざまな PowerShell タブで同じファイルが開かれているときに警告メッセージを表示するかどうかを指定します。 **$true** に設定している場合、複数のタブで同じファイルが開かれると、「このファイルのコピーが別の Windows PowerShell タブで開かれています。 このファイルを変更すると、開かれているすべてのファイルに影響します。」というメッセージが表示されます。 既定値は **$true** です。

```
# Enable the warning message when a file is
# opened in multiple PowerShell tabs.
$psISE.Options.ShowWarningForDuplicateFiles = $true

```

###  <a name="tc"></a> TokenColors
  Windows PowerShell ISE 2.0 以降でサポートされています。

 Windows PowerShell ISE のスクリプト ウィンドウの IntelliSense トークンの色を指定します。 このプロパティは、スクリプト ウィンドウのトークンの種類と色の名前/値のペアを含むディクショナリ オブジェクトです。 コンソール ウィンドウで IntelliSense トークンの色を変更する場合は、[ConsoleTokenColors](#contc) を参照してください。 色を既定値にリセットする場合、[RestoreDefaultTokenColors()](#rdtc) を参照してください。 次のトークンの色を設定することができます: Attribute、Command、CommandArgument、CommandParameter、Comment、GroupEnd、GroupStart、Keyword、LineContinuation、LoopLabel、Member、NewLine、Number、Operator、Position、StatementSeparator、String、Type、Unknown、Variable。

```
# Sets the color of commands to green.
$psISE.Options.TokenColors["Command"] = "green"
# Sets the color of keywords to magenta.
$psISE.Options.TokenColors["Keyword"] = "magenta"

```

###  <a name="uetsicpi"></a> UseEnterToSelectInConsolePaneIntellisense
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Enter キーを使用して、IntelliSense が提供するオプションをコンソール ウィンドウで選択できるようにするかどうかを指定します。 既定値は **$true** です。

```
# Turn off using the ENTER key to select an IntelliSense provided option in the Console pane.
$psISE.Options.UseEnterToSelectInConsolePaneIntellisense=$false

```

###  <a name="uetsispi"></a> UseEnterToSelectInScriptPaneIntellisense
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Enter キーを使用して、IntelliSense が提供するオプションをスクリプト ウィンドウで選択できるようにするかどうかを指定します。 既定値は **$true** です。

```
# Turn on using the Enter key to select an IntelliSense provided option in the Console pane.
$psISE.Options.UseEnterToSelectInConsolePaneIntellisense=$true

```

###  <a name="ulh"></a> UseLocalHelp
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 カーソルをキーワードに置いた状態で F1 キーを押したときに、ローカルにインストールされたヘルプまたはオンラインの TechNet ライブラリのヘルプを表示するかどうかを指定します。 **$true** に設定すると、ローカルにインストールされたヘルプ コンテンツがポップアップ ウィンドウに表示されます。 `Update-Help` コマンドを実行すると、ヘルプ ファイルをインストールすることができます。 **$false** に設定すると、ブラウザーが開き、TechNet ライブラリ内のページに移動します。

```
# Sets the option for the online help to be displayed.
$psISE.Options.UseLocalHelp=$false
# Sets the option for the local Help to be displayed.
$psISE.Options.UseLocalHelp=$true

```

###  <a name="vbc"></a> VerboseBackgroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示される詳細テキストの背景色を指定します。 これは **System.Windows.Media.Color** オブジェクトです。

```
# Changes the background color for verbose text to blue.
$psISE.Options.VerboseBackgroundColor ='#0000FF'
```

###  <a name="vfc"></a> VerboseForegroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示される詳細テキストの前景色を指定します。 これは **System.Windows.Media.Color** オブジェクトです。

```
# Changes the foreground color for verbose text to yellow.
$psISE.Options.VerboseForegroundColor =”yellow”
```

###  <a name="wbc"></a> WarningBackgroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 コンソール ウィンドウに表示される警告テキストの背景色を指定します。 これは **System.Windows.Media.Color** オブジェクトです。

```
# Changes the background color for warning text to blue.
$psISE.Options.WarningBackgroundColor ='#0000FF'
```

###  <a name="wfc"></a> WarningForegroundColor
  Windows PowerShell ISE 2.0 以降でサポートされています。

 出力ウィンドウに表示される警告テキストの前景色を指定します。 これは **System.Windows.Media.Color** オブジェクトです。

```
# Changes the foreground color for warning text to yellow.
$psISE.Options.WarningForegroundColor =”yellow”
```

###  <a name="xtc"></a> XmlTokenColors
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 Windows PowerShell ISE で表示される XML コンテンツのトークンの種類と色の名前/値のペアを含むディクショナリ オブジェクトを指定します。 次のトークンの色を設定することができます: Attribute、Command、CommandArgument、CommandParameter、Comment、GroupEnd、GroupStart、Keyword、LineContinuation、LoopLabel、Member、NewLine、Number、Operator、Position、StatementSeparator、String、Type、Unknown、Variable。 [RestoreDefaultXmlTokenColors()](#rdxtc) も参照してください。

```
# Sets the color of XML element names to green.
$psISE.Options.XmlTokenColors["ElementName"] = "green"
# Sets the color of XML comments to magenta.
$psISE.Options.XmlTokenColors["Comment"] = "magenta"

```

###  <a name="z"></a> ズーム
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 コンソール ウィンドウとスクリプト ウィンドウの両方でのテキストの相対サイズを指定します。 既定値は 100 です。 Windows PowerShell 内のテキストは、値が小さいと小さく表示され、値が大きいと大きく表示されます。 値は、20 ~ 400 の範囲の整数です。

```
# Changes the text in the Windows PowerShell ISE to be double its normal size.
$psISE.Options.Zoom = 200
```

## 参照
 [The Windows PowerShell ISE Scripting Object Model (Windows PowerShell ISE スクリプト オブジェクト モデル)](The-Windows-PowerShell-ISE-Scripting-Object-Model.md)
 [Windows PowerShell ISE Object Model Reference (Windows PowerShell ISE オブジェクト モデル リファレンス)](Windows-PowerShell-ISE-Object-Model-Reference.md)




<!--HONumber=Oct16_HO1-->


