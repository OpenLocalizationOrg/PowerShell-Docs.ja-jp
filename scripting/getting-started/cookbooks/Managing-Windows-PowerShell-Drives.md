---
title: "Windows PowerShell ドライブの管理"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: bd809e38-8de9-437a-a250-f30a667d11b4
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d266a109b1acd97c03594f988ce2fab3c697b80c

---

# Windows PowerShell ドライブの管理
*Windows PowerShell ドライブ*は、Windows PowerShell のファイル システム ドライブのようにアクセスできるデータ格納場所です。 Windows PowerShell プロバイダーは、ファイル システムのドライブ (C: および D: を含む)、レジストリ ドライブ (HKCU: および HKLM:)、および証明書ドライブ (Cert:) など、いくつかのドライブを作成して、独自の Windows PowerShell ドライブを作成できます。 これらのドライブは非常に便利ですが、Windows PowerShell 内でのみ利用可能です。 ファイル エクスプローラーや Cmd.exe など、他の Windows ツールを使用してアクセスすることはできません。

Windows PowerShell ドライブに対して使用できるコマンドには、**PSDrive** という名詞が使用されています。 Windows PowerShell セッションに存在する Windows PowerShell ドライブを一覧表示するには、**Get-PSDrive** コマンドレットを使用します。

```
PS> Get-PSDrive

Name       Provider      Root                                   CurrentLocation
----       --------      ----                                   ---------------
A          FileSystem    A:\
Alias      Alias
C          FileSystem    C:\                                 ...And Settings\me
cert       Certificate   \
D          FileSystem    D:\
Env        Environment
Function   Function
HKCU       Registry      HKEY_CURRENT_USER
HKLM       Registry      HKEY_LOCAL_MACHINE
Variable   Variable
```

表示されるドライブはシステムのドライブに応じて異なりますが、一覧は上記の **Get-PSDrive** コマンドの出力のようになります。

ファイル システムのドライブは、Windows PowerShell ドライブのサブセットです。 [プロバイダー] 列の FileSystem のエントリによって、ファイル システムのドライブを識別できます。 (Windows PowerShell のファイル システムのドライブは、Windows PowerShell FileSystem プロバイダーによってサポートされます。)

**Get-PSDrive** コマンドレットの構文を表示するには、**Get-Command** コマンドに **Syntax** パラメーターを指定して入力します。

```
PS> Get-Command -Name Get-PSDrive -Syntax
Get-PSDrive [[-Name] <String[]>] [-Scope <String>] [-PSProvider <String[]>] [-V
erbose] [-Debug] [-ErrorAction <ActionPreference>] [-ErrorVariable <String>] [-
OutVariable <String>] [-OutBuffer <Int32>]
```

**PSProvider** パラメーターでは、特定のプロバイダーによってサポートされている Windows PowerShell ドライブのみを表示できます。 たとえば、Windows PowerShell FileSystem プロバイダーによってサポートされる Windows PowerShell ドライブのみを表示するには、**Get-PSDrive** コマンドに **PSProvider** パラメーターと **FileSystem** の値を指定して入力します。

```
PS> Get-PSDrive -PSProvider FileSystem

Name       Provider      Root                                   CurrentLocation
----       --------      ----                                   ---------------
A          FileSystem    A:\
C          FileSystem    C:\                           ...nd Settings\PowerUser
D          FileSystem    D:\
```

レジストリ ハイブを表す Windows PowerShell ドライブを表示するには、**PSProvider** パラメーターを使用して、Windows PowerShell Registry プロバイダーによってサポートされる Windows PowerShell ドライブのみを表示します。

<pre>PS> Get-PSDrive -PSProvider Registry Name       Provider      Root                                   CurrentLocation ----       --------      ----                                   --------------- HKCU       Registry      HKEY_CURRENT_USER HKLM       Registry      HKEY_LOCAL_MACHINE</pre>

また、Windows PowerShell ドライブに標準的な Location コマンドレットを使用することもできます。

<pre>PS> Set-Location HKLM:\SOFTWARE PS> Push-Location .\Microsoft PS> Get-Location Path ---- HKLM:\SOFTWARE\Microsoft</pre>

### 新しい Windows PowerShell ドライブの追加 (New-PSDrive)
**New-PSDrive** コマンドを使用して、独自の Windows PowerShell ドライブを追加できます。 **New-PSDrive** コマンドの構文を取得するには、**Get-Command** コマンドに **Syntax** パラメーターを指定して入力します。

```
PS> Get-Command -Name New-PSDrive -Syntax
New-PSDrive [-Name] <String> [-PSProvider] <String> [-Root] <String> [-Descript
ion <String>] [-Scope <String>] [-Credential <PSCredential>] [-Verbose] [-Debug
] [-ErrorAction <ActionPreference>] [-ErrorVariable <String>] [-OutVariable <St
ring>] [-OutBuffer <Int32>] [-WhatIf] [-Confirm]
```

新しい Windows PowerShell ドライブを作成するには、3 つのパラメーターを指定する必要があります。

-   ドライブの名前 (有効な Windows PowerShell の名前を使用できます)

-   PSProvider (ファイル システムの場所には "FileSystem" を、レジストリの場所には "Registry" を使用します)

-   ルート、つまり、新しいドライブのルートのパス

たとえば、**C:\Program Files\Microsoft Office\OFFICE11** のように、コンピューターの Microsoft Office アプリケーションを含むフォルダーにマップされる、"Office" という名前のドライブを作成できます。 ドライブを作成するには、次のコマンドを入力します。

```
PS> New-PSDrive -Name Office -PSProvider FileSystem -Root "C:\Program Files\Micr
osoft Office\OFFICE11"

Name       Provider      Root                                   CurrentLocation
----       --------      ----                                   ---------------
Office     FileSystem    C:\Program Files\Microsoft Offic...
```

> [!NOTE]
> 一般的に、パスは大文字と小文字が区別されません。

すべての Windows PowerShell ドライブと同様に、名前の後にコロン (**:**) を指定して、新しい Windows PowerShell ドライブを参照します。

Windows PowerShell ドライブにより、多数のタスクが簡単になります。 たとえば、Windows レジストリ内の最も重要なキーのいくつかが、極端に長いパスを持っていて、アクセスが煩雑で覚えにくいものがあります。 重要な構成情報が存在する **HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion**します。 CurrentVersion レジストリ キーの項目を表示して変更するために、次のように入力して、そのキーのルートになる Windows PowerShell ドライブを作成できます。

<pre>PS> New-PSDrive -Name cvkey -PSProvider Registry -Root HKLM\Software\Microsoft\W indows\CurrentVersion Name       Provider      Root                                   CurrentLocation ----       --------      ----                                   --------------- cvkey      Registry      HKLM\Software\Microsoft\Windows\...</pre>

**cvkey:** ドライブに場所を移動するには、他のドライブと同様、次のように入力します。

`PS> cd cvkey:`

または

<pre>PS> Set-Location cvkey: -PassThru Path ---- cvkey:\</pre>

New-PsDrive コマンドレットが、現在の Windows PowerShell セッションのみに新しいドライブを追加します。 Windows PowerShell ウィンドウを閉じると、新しいドライブは失われます。 Windows PowerShell ドライブを保存するには、Export-Console コマンドレットを使用して、現在の Windows PowerShell セッションをエクスポートし、PowerShell.exe **PSConsoleFile** パラメーターを使用してインポートします。 または、新しいドライブを Windows PowerShell プロファイルに追加します。

### Windows PowerShell ドライブの削除 (Remove-PSDrive)
**Remove-PSDrive** コマンドレットを使用して、Windows PowerShell からドライブを削除できます。 **Remove-PSDrive** コマンドレットは簡単に使用できます。特定の Windows PowerShell ドライブを削除するには、Windows PowerShell ドライブの名前を指定するだけです。

たとえば、**New-PSDrive** のトピックでは、**Office:** という Windows PowerShell ドライブを追加しました。このドライブを削除するには、次のように入力します。

```
PS> Remove-PSDrive -Name Office
```

**New-PSDrive** トピックで使用した **cvkey:** という Windows PowerShell ドライブを削除するには、次のコマンドを使用します。

```
PS> Remove-PSDrive -Name cvkey
```

Windows PowerShell ドライブを削除するのは簡単ですが、そのドライブにいる間は削除できません。 たとえば、次のように入力します。

```
PS> cd office:
PS Office:\> remove-psdrive -name office
Remove-PSDrive : Cannot remove drive 'Office' because it is in use.
At line:1 char:15
+ remove-psdrive  <<<< -name office
```

### Windows PowerShell の外部のドライブを追加および削除する
Windows PowerShell は、マップされるネットワーク ドライブ、挿入される USB ドライブ、削除されるドライブ (Windows Script Host (WSH) スクリプトから **net use** コマンドまたは **WScript.NetworkMapNetworkDrive**、**RemoveNetworkDrive** メソッドを使用) など、Windows に追加または削除されるファイル システムのドライブを検出します。




<!--HONumber=Oct16_HO1-->


