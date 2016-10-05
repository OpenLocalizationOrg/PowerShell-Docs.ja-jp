---
title: "ISEMenuItem オブジェクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: a16660bd-0aee-46fd-ac17-3f022165d089
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e067519d58ca381fc4e5c746fe9bbd16bdc68c60

---

# ISEMenuItem オブジェクト
  **ISEMenuItem** オブジェクトは Microsoft.PowerShell.Host.ISE.ISEMenuItem クラスのインスタンスです。 **[アドオン]** メニューにあるすべてのオブジェクトは、**Microsoft.PowerShell.Host.ISE.ISEMenuItem** クラスのインスタンスです。

## プロパティ

###  <a name="DisplayName"></a> 表示名
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 メニュー項目の名前を表示する読み取り専用プロパティ。

```
# Get the display name of the Add-ons menu item
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P")
$psISE.CurrentPowerShellTab.AddOnsMenu.DisplayName

```

###  <a name="Action"></a> アクション
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 スクリプトのブロックを取得する読み取り専用プロパティ。 メニュー項目をクリックすると、アクションが呼び出されます。

```
# Get the action associated with the first submenu item.
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P")
$psISE.CurrentPowerShellTab.AddOnsMenu.Submenus[0].Action

# Invoke the script associated with the first submenu item 
$psISE.CurrentPowerShellTab.AddOnsMenu.Submenus[0].Action.Invoke()
```

###  <a name="Shortcut"></a> ショートカット
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 メニュー項目の Windows 入力用ショートカット キーを取得する読み取り専用プロパティ。

```
# Get the shortcut for the first submenu item.
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P")
$psISE.CurrentPowerShellTab.AddOnsMenu.Submenus[0].Shortcut
```

###  <a name="Submenus"></a> サブメニュー
  Windows PowerShell ISE 2.0 以降でサポートされています。 

 メニュー項目の[サブメニューの一覧](The-ISEMenuItemCollection-Object.md)を取得する読み取り専用プロパティ。

```
# List the submenus of the Add-ons menu
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P")
$psISE.CurrentPowerShellTab.AddOnsMenu.Submenus
```

## スクリプトの例
 [アドオン] メニューとそのスクリプト可能なプロパティの使用をさらに理解するには、次のスクリプトの例に目を通してください。

```

# This is a scripting example that shows the use of the Add-ons menu.
# Clear the Add-ons menu if any entries currently exist
$psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Clear()

# Add an Add-ons menu item with an shortcut and fast access key.
# Note the use of “_”  as opposed to the “&” for mapping to the fast access key letter for the menu item.
$menuAdded = $psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("_Process",{get-process},"Alt+P") 
# Add a nested menu – a parent and a child submenu item. 
$parentAdded = $psISE.CurrentPowerShellTab.AddOnsMenu.SubMenus.Add("Parent",$null,$null) 
$parentAdded.SubMenus.Add("_Dir",{dir},"Alt+D")

```

## 参照
 [ISEMenuItemCollection オブジェクト](The-ISEMenuItemCollection-Object.md) 
 [Windows PowerShell ISE スクリプト オブジェクト モデル](The-Windows-PowerShell-ISE-Scripting-Object-Model.md) 
 [Windows PowerShell ISE オブジェクト モデル リファレンス](Windows-PowerShell-ISE-Object-Model-Reference.md) 
 [ISE オブジェクト モデル階層](The-ISE-Object-Model-Hierarchy.md)

  



<!--HONumber=Oct16_HO1-->


