---
title: "PowerShellTab オブジェクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a9b58556-951b-4f48-b3ae-b351b7564360
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: f3f2d27c3c82406f8e8967fd1784a6e07579c1fa

---

# PowerShellTab オブジェクト
  **PowerShellTab** オブジェクトは、Windows PowerShell ランタイム環境を表します。

## メソッド

###  <a name="invoke"></a> 起動 \ (スクリプト \)
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 指定したスクリプトを [PowerShell] タブで実行します。

> [!NOTE]
>  このメソッドは、その他の [PowerShell] タブでのみ機能し、実行元となる [PowerShell] タブでは機能しません。 このコマンドはオブジェクトや値を返しません。 このコードによって変数が変更されると、その変更はコマンドが呼び出されたタブで保持されます。

 **Script** - System.Management.Automation.ScriptBlock または文字列。実行するスクリプト ブロック。

```
# Manually create a second PowerShell tab before running this script.
# Return to the first PowerShell tab and type the following command
$psise.PowerShellTabs[1].Invoke({dir})
```

### InvokeSynchronous\( Script, \[useNewScope\], millisecondsTimeout \)
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。 

 指定したスクリプトを [PowerShell] タブで実行します。

> [!NOTE]
>  このメソッドは、その他の [PowerShell] タブでのみ機能し、実行元となる [PowerShell] タブでは機能しません。 スクリプト ブロックが実行され、スクリプトから返される値はコマンドを呼び出した実行環境に返されます。 コマンドが **millesecondsTimeout** の値で指定した時間内に完了しない場合、コマンドが失敗して例外 "処理がタイムアウトになりました。" が発生します。

 **Script** - System.Management.Automation.ScriptBlock または文字列。実行するスクリプト ブロック。

 **\[useNewScope\]** -オプションを示すブール値の既定値 **$true**
 場合に設定 **$true**, 、コマンドを実行する新しいスコープを作成します。 コマンドで指定されている [PowerShell] タブのランタイム環境は変更されません。

 **\[millisecondsTimeout\]** -既定値は省略可能な整数 **500**します。
指定した時間内にコマンドが完了しない場合、コマンドによって **TimeoutException** が生成され、"処理がタイムアウトになりました。" というメッセージが表示されます。

```
# create a new PowerShell tab and then switch back to the first
$PSise.PowerShellTabs.Add()
$psISE.PowerShellTabs.SetSelectedPowerShellTab($psISE.PowerShellTabs[0]) 

# Invoke a simple command on the other tab, in its own scope
$psISE.PowerShellTabs[1].InvokeSynchronous('$x=1',$false)
# You can switch to the other tab and type “$x” to see that the value is saved there.

# This example sets a value in the other tab (in a different scope) 
# and returns it through the pipeline to this tab to store in $a
$a=$psISE.PowerShellTabs[1].InvokeSynchronous('$z=3;$z') 
$a

# This example runs a command that takes longer than the allowed timeout value
# and measures how long it runs so that you can see the impact
measure-command {$psISE.PowerShellTabs[1].InvokeSynchronous("sleep 10",$false,5000)}

```

## プロパティ

###  <a name="AddOnsMenu"></a> AddOnsMenu
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 [PowerShell] タブのアドオン メニューを取得する読み取り専用のプロパティです。

```
# Clear the Add-ons menu if one exists.
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()
# Create an AddOns menu with an accessor.
# Note the use of "_"  as opposed to the "&" for mapping to the fast key letter for the menu item.
$menuAdded = $psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P") 
# Add a nested menu. 
$parentAdded = $psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("Parent",$null,$null) 
$parentAdded.SubMenus.Add("_Dir",{dir},"Alt+D")
# Show the Add-ons menu on the current PowerShell tab.
$psISE.CurrentPowerShellTab.AddOnsMenu
```

###  <a name="CanExecute"></a> CanInvoke
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 スクリプトを [Invoke( Script )](#invoke) メソッドで呼び出すことが可能な場合は **$true** を返す、読み取り専用のブール型プロパティです。

```
# CanInvoke will be false if the PowerShell
# tab is running a script that takes a while, and you
# check its properties from another PowerShell tab. It is
# always false if checked on the current PowerShell tab. 
# Manually create a second PowerShell tab before running this script.
# Return to the first tab and type
$secondTab = $psise.PowerShellTabs[1] 
$secondTab.CanInvoke 
$secondTab.Invoke({sleep 20})
$secondTab.CanInvoke

```

###  <a name="Commandpane"></a> Consolepane
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。  Windows PowerShell ISE 2.0 では、このパラメータの名前は **CommandPane** でした。

 コンソール ウィンドウの [editor](../ise/The-ISEEditor-Object.md) オブジェクトを取得する読み取り専用のプロパティです。

```
# Gets the Console Pane editor.
$psISE.CurrentPowerShellTab.ConsolePane

```

###  <a name="Displayname"></a> 表示名
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 [PowerShell] タブに表示されるテキストを取得または設定する読み取り/書き込みプロパティです。 既定では、タブ名は "PowerShell #" となり、「#」には番号が入ります。

```
$newTab = $psise.PowerShellTabs.Add()
# Change the DisplayName of the new PowerShell tab. 
$newTab.DisplayName="Brand New Tab"
```

###  <a name="ExpandedScript"></a> ExpandedScript
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 スクリプト ウィンドウが展開されているか非表示かどうかを特定する読み取り/書き込みブール型プロパティです。

```
# Toggle the expanded script property to see its effect.
$PSise.CurrentPowerShellTab.ExpandedScript=!$PSise.CurrentPowerShellTab.ExpandedScript

```

###  <a name="Files"></a> ファイル
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 [PowerShell] タブで開かれている[スクリプト ファイルのコレクション](../ise/The-ISEFileCollection-Object.md)を取得する読み取り専用のプロパティです。

```
$newFile = $psISE.CurrentPowerShellTab.Files.Add()
$newFile.Editor.Text = "a`r`nb" 
# Gets the line count
$newFile.Editor.LineCount
```

###  <a name="Output"></a> 出力
  この機能は、Windows PowerShell ISE 2.0 に存在しますが、それよりも後のバージョンの ISE では削除されているか、名前が変更されています。  Windows PowerShell ISE 2.0 より後のバージョンでは、**ConsolePane** オブジェクトを同じ目的で使用できます。

 現在の[エディター](../ise/The-ISEEditor-Object.md)の出力ウィンドウが取得する読み取り専用のプロパティです。

```
# Clears the text in the Output pane.
$psise.CurrentPowerShellTab.output.clear()
```

###  <a name="Prompt"></a> プロンプト
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 現在のプロンプト テキストを取得する読み取り専用のプロパティです。 注: **Prompt** 関数は、ユーザーのプロファイルで上書きできます。 結果が単純な文字列以外の場合、このプロパティは何も返しません。

```
# Gets the current prompt text.
$psISE.CurrentPowerShellTab.Prompt
```

###  <a name="ShowCommands"></a> ShowCommands
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。 

 コマンド ウィンドウが現在表示されているかどうかを示す読み取り/書き込みプロパティです。

```
# Gets the current status of the Commands pane and stores it in the $a variable
$a = $psISE.CurrentPowerShellTab.ShowCommands
# if $a is $false, then turn the Commands pane on by changing the value to $True
if (!$a) {$psISE.CurrentPowerShellTab.ShowCommands=$True}
```

###  <a name="StatusText"></a> StatusText
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 **PowerShellTab** ステータス テキストを取得する読み取り専用のプロパティです。

```
# Gets the current status text,
$psISE.CurrentPowerShellTab.StatusText
```

###  <a name="HorizontalAddOnToolsPaneOpened"></a> HorizontalAddOnToolsPaneOpened
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。 

 水平方向のアドオン ツール ウィンドウが現在開いているかどうかを示す読み取り 専用のプロパティです。

```
# Gets the current state of the horizontal Add-ons tool pane. 
$psISE.CurrentPowerShellTab.HorizontalAddOnToolsPaneOpened
```

###  <a name="VerticalAddOnToolsPaneOpened"></a> **VerticalAddOnToolsPaneOpened**
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。 

 垂直方向のアドオン ツール ウィンドウが現在開いているかどうかを示す読み取り 専用のプロパティです。

```
# Turns on the Commands pane
$psISE.CurrentPowerShellTab.ShowCommands=$True
# Gets the current state of the vertical Add-ons tool pane. 
$psISE.CurrentPowerShellTab.HorizontalAddOnToolsPaneOpened
```

## 参照
 [PowerShellTabCollection オブジェクト](The-PowerShellTabCollection-Object.md) 
 [Windows PowerShell ISE スクリプト オブジェクト モデル](../ise/The-Windows-PowerShell-ISE-Scripting-Object-Model.md) 
 [Windows PowerShell ISE オブジェクト モデル リファレンス](../ise/Windows-PowerShell-ISE-Object-Model-Reference.md) 
 [ISE オブジェクト モデル階層](../ise/The-ISE-Object-Model-Hierarchy.md)

  



<!--HONumber=Oct16_HO1-->


