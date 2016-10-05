---
title: "ISEFileCollection オブジェクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 0f86a427-ea38-4bce-85f8-06c98d30d508
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: c334a38d6686be45101f4569f38411e9703c8fea

---

# ISEFileCollection オブジェクト
  **ISEFileCollection** オブジェクトは **ISEFile** オブジェクトのコレクションです。 たとえば $psISE.CurrentPowerShellTab.Files コレクションです。

## メソッド

### Add\( \[fullPath\] \)
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 新しい無題ファイルを作成して返し、コレクションに追加します。  **IsUntitled** 、新しく作成されたファイルのプロパティは、 **$true**します。

 **\[fullPath\]** -省略可能ファイルの完全指定パスを表す文字列します。 **fullPath** パラメーターと相対パスを含める、または完全パスではなくファイル名を使用すると、例外が生成されます。

```
# Adds a new untitled file to the collection of files in the current PowerShell tab.
$newFile = $psISE.CurrentPowerShellTab.Files.Add()

# Adds a file specified by its full path to the collection of files in the current PowerShell tab.
$psISE.CurrentPowerShellTab.Files.Add("$pshome\Examples\profile.ps1")

```

### Remove\( File, \[Force\] \)
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 現在の PowerShell タブから指定されたファイルを削除します。

 **ファイル** – をコレクションから削除する文字列、ISEFile ファイルです。 ファイルが保存されていない場合、このメソッドにより例外がスローされます。 **Force** スイッチ パラメーターを使用すると、保存されていないファイルが強制的に削除されます。

 **\[Force\]** – 省略可能なブール値に設定 **$true**, 、最後の使用後に保存されていない場合でも、ファイルを削除する権限を付与します。 既定値は **$false** です。

```
# Removes the first opened file from the file collection associated with the current PowerShell tab.
# If the file has not yet been saved, then an exception is generated.
$firstfile = $psISE.CurrentPowerShellTab.Files[0]
$psISE.CurrentPowerShellTab.Files.Remove($firstfile)

# Removes the first opened file from the file collection associated with the current PowerShell tab, even if it has not been saved.
$firstfile = $psISE.CurrentPowerShellTab.Files[0]
$psISE.CurrentPowerShellTab.Files.Remove($firstfile, $true)
```

### SetSelectedFile\ (selectedFile \)
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 **selectedFile** パラメーターにより指定されたファイルを選択します。

 **selectedFile** – Microsoft.PowerShell.Host.ISE.ISEFile、ISEFile ファイルを選択します。

```

# Selects the specified file.
$firstfile = $psISE.CurrentPowerShellTab.Files[0]
$psISE.CurrentPowerShellTab.Files.SetSelectedFile($firstfile)

```

## 参照
 [ISEFile オブジェクト](The-ISEFile-Object.md) 
 [Windows PowerShell ISE のスクリプト オブジェクト モデル](The-Windows-PowerShell-ISE-Scripting-Object-Model.md) 
 [Windows PowerShell ISE オブジェクト モデル リファレンス](Windows-PowerShell-ISE-Object-Model-Reference.md) 
 [ISE オブジェクト モデル階層](The-ISE-Object-Model-Hierarchy.md)

  



<!--HONumber=Oct16_HO1-->


