---
title: "レジストリ エントリの操作"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: fd254570-27ac-4cc9-81d4-011afd29b7dc
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: cdc7f45c9fa8a6bf748a52b460a1ac190d283971

---

# レジストリ エントリの操作
レジストリ エントリはキーのプロパティであり直接参照できないため、利用するときは少し異なる方法を取る必要があります。

### レジストリ エントリの一覧表示
レジストリ エントリを確認するには、多くのさまざまな方法があります。 最も簡単な方法は、キーに関連付けられているプロパティの名前を取得することです。 たとえば、レジストリ キーのエントリの名前を表示する **HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion**, を使用して **Get-item**します。 レジストリ キーは、キーのレジストリ エントリの一覧であり、"Property"という汎用的な名前のプロパティを持っています。 次のコマンドは、Property プロパティを選択し、一覧に表示されるように項目を拡張します。

```
PS> Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion | Select-Object -ExpandProperty Property
DevicePath
MediaPathUnexpanded
ProgramFilesDir
CommonFilesDir
ProductId
```

レジストリ エントリをさらに読みやすい形式で表示するには、**Get-ItemProperty** を使用します。

```
PS> Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion

PSPath              : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SO
                      FTWARE\Microsoft\Windows\CurrentVersion
PSParentPath        : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SO
                      FTWARE\Microsoft\Windows
PSChildName         : CurrentVersion
PSDrive             : HKLM
PSProvider          : Microsoft.PowerShell.Core\Registry
DevicePath          : C:\WINDOWS\inf
MediaPathUnexpanded : C:\WINDOWS\Media
ProgramFilesDir     : C:\Program Files
CommonFilesDir      : C:\Program Files\Common Files
ProductId           : 76487-338-1167776-22465
WallPaperDir        : C:\WINDOWS\Web\Wallpaper
MediaPath           : C:\WINDOWS\Media
ProgramFilesPath    : C:\Program Files
PF_AccessoriesName  : Accessories
(default)           :
```

キーの Windows PowerShell 関連のプロパティには、**PSPath**、**PSParentPath**、**PSChildName**、および **PSProvider** のように、すべて先頭に "PS" が付きます。

現在の場所を参照するには、"**.**" 表記法を使用できます。 **Set-Location** を使用して、まず **CurrentVersion** レジストリのコンテナーに移動します。

```
Set-Location -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion
```

または、**Set-Location** を指定して組み込みの HKLM PSDrive を使用できます。

```
Set-Location -Path hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion
```

現在の場所に対して "**.**" 表記法を使用して、完全パスを指定しないでプロパティの一覧を表示することができます。

```
PS> Get-ItemProperty -Path .
...
DevicePath          : C:\WINDOWS\inf
MediaPathUnexpanded : C:\WINDOWS\Media
ProgramFilesDir     : C:\Program Files
...
```

パスの展開仕組みは同じとファイル システム内でこの場所から取得できます、 **ItemProperty** の **HKLM:\\SOFTWARE\\Microsoft\\Windows\\Help** を使用して **Get-itemproperty-パス.\\Help**します。

### 1 つのレジストリ エントリの取得
レジストリ キーの特定のエントリを取得する場合は、いくつかの可能なアプローチのいずれかを使用できます。 この例の値を検索する **DevicePath** で **HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion**します。

**Get-ItemProperty** を使用する場合に、**Path** パラメーターを使用してキーの名前を指定し、**Name** パラメーターを使用して **DevicePath** のエントリの名前を指定します。

```
PS> Get-ItemProperty -Path HKLM:\Software\Microsoft\Windows\CurrentVersion -Name DevicePath

PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\Software\
               Microsoft\Windows\CurrentVersion
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\Software\
               Microsoft\Windows
PSChildName  : CurrentVersion
PSDrive      : HKLM
PSProvider   : Microsoft.PowerShell.Core\Registry
DevicePath   : C:\WINDOWS\inf
```

このコマンドは、標準の Windows PowerShell のプロパティと **DevicePath** プロパティを返します。

> [!NOTE]
> **Get-ItemProperty** に **Filter**、**Include**、**Exclude** の各パラメーターがあっても、プロパティ名によるフィルター処理に使用することはできません。 これらのパラメーターはレジストリ キー (項目のパス) を参照し、レジストリ エントリ (項目のプロパティ) を参照するのではありません。

別のオプションとして、Reg.exe コマンド ライン ツールを使用することもできます。 reg.exe のヘルプを表示するには、コマンド プロンプトで「**reg.exe /?**」と入力します。 at a command prompt. DevicePath エントリを検索するには、次のコマンドに示すように reg.exe を使用します。

```
PS> reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion /v DevicePath

! REG.EXE VERSION 3.0

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion
    DevicePath  REG_EXPAND_SZ   %SystemRoot%\inf
```

また、**WshShell COM** オブジェクトを使用していくつかのレジストリ エントリを検索することもできますが、この方法は、大きなバイナリ データや、名前に"\" を含むレジストリ エントリでは利用できません。 プロパティ名を区切り記号 \ と共に項目のパスに追加します。

```
PS> (New-Object -ComObject WScript.Shell).RegRead("HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\DevicePath")
%SystemRoot%\inf
```

### 新しいレジストリ エントリの作成
"PowerShellPath" という名前の新しいエントリを **CurrentVersion** キーに追加するには、キーのパス、エントリ名、エントリの値を指定して **New-ItemProperty** を使用します。 この例では、Windows PowerShell の変数 **$PSHome** の値を取得します。これは、Windows PowerShell のインストール ディレクトリへのパスを保存します。

キーに新しいエントリを追加するには、次のコマンドを使用します。このコマンドは、新しいエントリに関する情報も返します。

```
PS> New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PowerShellPath -PropertyType String -Value $PSHome

PSPath         : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWAR
                 E\Microsoft\Windows\CurrentVersion
PSParentPath   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWAR
                 E\Microsoft\Windows
PSChildName    : CurrentVersion
PSDrive        : HKLM
PSProvider     : Microsoft.PowerShell.Core\Registry
PowerShellPath : C:\Program Files\Windows PowerShell\v1.0
```

**PropertyType** は、次の表の **Microsoft.Win32.RegistryValueKind** 列挙体のメンバーの名前にする必要があります。

|PropertyType の値|意味|
|----------------------|-----------|
|Binary|バイナリ データ|
|DWord|有効な UInt32 である数字|
|ExpandString|動的に展開される環境変数を含むことができる文字列|
|MultiString|複数行文字列|
|String|任意の文字列値|
|QWord|8 バイトのバイナリ データ|

> [!NOTE]
> **Path** パラメーターに値の配列を指定して、レジストリ エントリを複数の場所に追加できます。

```
New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion, HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PowerShellPath -PropertyType String -Value $PSHome
```

また、**Force** パラメーターを **New-ItemProperty** コマンドに追加して、既存のレジストリ エントリの値を上書きすることもできます。

### レジストリ エントリの名前変更
**PowerShellPath** エントリの名前を "PSHome" に変更するには、**Rename-ItemProperty** を使用します。

```
Rename-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PowerShellPath -NewName PSHome
```

名前が変更された値を表示するには、**PassThru** パラメーターをコマンドに追加します。

```
Rename-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PowerShellPath -NewName PSHome -passthru
```

### レジストリ エントリの削除
PSHome と PowerShellPath の両方のレジストリ エントリを削除するには、**Remove-ItemProperty** を使用します。

```
Remove-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PSHome
Remove-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion -Name PowerShellPath
```




<!--HONumber=Oct16_HO1-->


