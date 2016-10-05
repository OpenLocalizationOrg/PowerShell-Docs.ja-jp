---
title: "ISEAddOnTool オブジェクト"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: ce84d8bc-07ba-41f6-bdde-d6f3fddcd1e3
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e63809763808836af9f468c2ac55ede42836d6b2

---

# ISEAddOnTool オブジェクト
  **ISEAddonTool** オブジェクトは、Windows PowerShell ISE に追加の機能を提供する、インストール済みのアドオン ツールを表します。 例としては、**[表示]**、**[コマンド アドオンの表示]** の順にクリックして表示できる**コマンド** ツールです。 このツールには、利用可能なさまざまな **ISEAddOnTool** オブジェクトを操作することでアクセスできます。

 各アドオン ツールは、垂直方向のウィンドウまたは水平方向のウィンドウのいずれかと関連付けることができます。 垂直方向のウィンドウは、Windows PowerShell ISE の右端にドッキングされています。 水平方向のウィンドウは、下端にドッキングされています。

 Windows PowerShell ISE の各 PowerShell タブには、インストール済みのアドオン ツールの固有のセットを含めることができます。 現在選択しているタブで使用できるツールのコレクション、または [$psISE.PowerShellTabs](The-PowerShellTabCollection-Object.md) コレクション オブジェクトのいずれかの **PowerShellTab** オブジェクトの同じプロパティのコレクションにアクセスするには、[$psISE.CurrentPowerShellTab.HorizontalAddOnTools](The-ISEAddOnToolCollection-Object.md) および [$psISE.CurrentPowerShellTab.VerticalAddOnTools](The-ISEAddOnToolCollection-Object.md) を参照してください。

## メソッド
 このクラスのオブジェクトで使用できる Windows PowerShell ISE 固有のメソッドはありません。

## プロパティ

###  <a name="Control"></a> コントロール
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 **コントロール** プロパティは、コマンド アドオン ツールの詳細への読み取りアクセスを提供します。

```
# View the properties of the Commands add-on tool.
# (assumes that it is visible in the vertical pane)
$psISE.CurrentVisibleVerticalTool.Control
HostObject                  : Microsoft.PowerShell.Host.ISE.ObjectModelRoot
Content                     :
HasContent                  :
ContentTemplate             :
ContentTemplateSelector     :
ContentStringFormat         :
BorderBrush                 :
BorderThickness             :
Background                  :
Foreground                  :
FontFamily                  :
FontSize                    :
FontStretch                 :
FontStyle                   :
FontWeight                  :
HorizontalContentAlignment  :
VerticalContentAlignment    :
TabIndex                    :
IsTabStop                   :
Padding                     :
Template                    : System.Windows.Controls.ControlTemplate
Style                       :
OverridesDefaultStyle       :
UseLayoutRounding           :
Triggers                    : {}
TemplatedParent             :
Resources                   : {System.Windows.Controls.TabItem}
DataContext                 :
BindingGroup                :
Language                    :
Name                        :
Tag                         :
InputScope                  :
ActualWidth                 : 370.75
ActualHeight                : 676.559097412109
LayoutTransform             :
Width                       :
MinWidth                    :
MaxWidth                    :
Height                      :
MinHeight                   :
MaxHeight                   :
FlowDirection               : LeftToRight
Margin                      :
HorizontalAlignment         :
VerticalAlignment           :
FocusVisualStyle            :
Cursor                      :
ForceCursor                 :
IsInitialized               : True
IsLoaded                    :
ToolTip                     :
ContextMenu                 :
Parent                      :
HasAnimatedProperties       :
InputBindings               :
CommandBindings             :
AllowDrop                   :
DesiredSize                 : 227.66,676.559097412109
IsMeasureValid              : True
IsArrangeValid              : True
RenderSize                  : 370.75,676.559097412109
RenderTransform             :
RenderTransformOrigin       :
IsMouseDirectlyOver         : False
IsMouseOver                 : False
IsStylusOver                : False
IsKeyboardFocusWithin       : False
IsMouseCaptured             :
IsMouseCaptureWithin        : False
IsStylusDirectlyOver        : False
IsStylusCaptured            :
IsStylusCaptureWithin       : False
IsKeyboardFocused           : False
IsInputMethodEnabled        :
Opacity                     :
OpacityMask                 :
BitmapEffect                :
Effect                      :
BitmapEffectInput           :
CacheMode                   :
Uid                         :
Visibility                  : Visible
ClipToBounds                : False
Clip                        :
SnapsToDevicePixels         : False
IsFocused                   :
IsEnabled                   :
IsHitTestVisible            :
IsVisible                   : True
Focusable                   :
PersistId                   : 1
IsManipulationEnabled       :
AreAnyTouchesOver           : False
AreAnyTouchesDirectlyOver   :
AreAnyTouchesCapturedWithin : False
AreAnyTouchesCaptured       :
TouchesCaptured             : {}
TouchesCapturedWithin       : {}
TouchesOver                 : {}
TouchesDirectlyOver         : {}
DependencyObjectType        : System.Windows.DependencyObjectType
IsSealed                    : False
Dispatcher                  : System.Windows.Threading.Dispatcher

```

###  <a name="IsVisible"></a> IsVisible
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 割り当てられているウィンドウにアドオン ツールが現在表示されているかどうかを示すブール型プロパティ。 表示されている場合は、**IsVisible** プロパティを **$false** を設定すると、ツールを非表示にすることができます。**IsVisible** プロパティを **$true** に設定すると、PowerShell でアドオン ツールが表示されます。 アドオン ツールを非表示にすると、**CurrentVisibleHorizontalTool** または **CurrentVisibleVerticalTool** オブジェクトからアクセスできなくなるため、これらのオブジェクトのこのプロパティを使用して表示するようにすることはできません。

```
# Hide the current tool in the vertical tool pane
$psISE.CurrentVisibleVerticalTool.IsVisible = $false
# Show the first tool on the currently selected PowerShell tab
$psISE.CurrentPowerShellTab.VerticalAddOnTools[0].IsVisible=$true

```

###  <a name="name"></a> 名
  Windows PowerShell ISE 3.0 以降でサポートされており、それよりも前のバージョンには存在しません。

 アドオン ツールの名前を取得する読み取り専用プロパティ。

```
# Gets the name of the visible vertical pane add-on tool.
$psISE.CurrentVisibleVerticalTool.name
Commands

```

## 参照
- [ISEAddOnToolCollection オブジェクト](The-ISEAddOnToolCollection-Object.md)
- [Windows PowerShell ISE のスクリプト オブジェクト モデル](The-Windows-PowerShell-ISE-Scripting-Object-Model.md)
- [Windows PowerShell ISE オブジェクト モデル リファレンス](Windows-PowerShell-ISE-Object-Model-Reference.md)
- [ISE オブジェクト モデル階層](The-ISE-Object-Model-Hierarchy.md)




<!--HONumber=Oct16_HO1-->


