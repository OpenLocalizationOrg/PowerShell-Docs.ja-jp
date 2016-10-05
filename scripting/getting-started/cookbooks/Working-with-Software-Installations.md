---
title: "ソフトウェア インストールの操作"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 51a12fe9-95f6-4ffc-81a5-4fa72a5bada9
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 13fdac5369d70289d7a0b5115a04879f707e47fc

---

# ソフトウェア インストールの操作
Windows インストーラーを使用するように設計されているアプリケーションは、WMI の **Win32_Product** クラスからアクセスできますが、今日使用されているすべてのアプリケーションが Windows インストーラーを使用しているわけではありません。 Windows インストーラーは、インストール可能なアプリケーションを操作するための標準的な手法を最も広範囲に提供しているため、主にこれらのアプリケーションについて説明します。 代替のセットアップ ルーチンを使用するアプリケーションは、通常 Windows インストーラーによって管理されません。 これらのアプリケーションを処理する具体的な方法は、インストーラーのソフトウェアとアプリケーションの開発者によってなされた決定によって異なります。

> [!NOTE]
> コンピューターにアプリケーションのファイルをコピーすることによってインストールされているアプリケーションは、通常、ここで紹介されている手法を使用してで管理することはできません。 これらのアプリケーションは、「ファイルとフォルダーの操作」セクションで説明した手法を使用することで、ファイルやフォルダーとして管理できます。

### Windows インストーラー アプリケーションを一覧表示する
ローカルまたはリモート システムに Windows インストーラーを使用してインストールされているアプリケーションの一覧を表示するには、次の単純な WMI クエリを使用します。

```
PS> Get-WmiObject -Class Win32_Product -ComputerName .
IdentifyingNumber : {7131646D-CD3C-40F4-97B9-CD9E4E6262EF}
Name              : Microsoft .NET Framework 2.0
Vendor            : Microsoft Corporation
Version           : 2.0.50727
Caption           : Microsoft .NET Framework 2.0
```

表示するには、すべての Win32_Product オブジェクトのプロパティを表示の値を持つ Format-list コマンドレットなどの書式設定コマンドレットのプロパティのパラメーターを使用します。 \ * (すべて)。

```
PS> Get-WmiObject -Class Win32_Product -ComputerName . | Where-Object -FilterScript {$_.Name -eq "Microsoft .NET Framework 2.0"} | Format-List -Property *
Name              : Microsoft .NET Framework 2.0
Version           : 2.0.50727
InstallState      : 5
Caption           : Microsoft .NET Framework 2.0
Description       : Microsoft .NET Framework 2.0
IdentifyingNumber : {7131646D-CD3C-40F4-97B9-CD9E4E6262EF}
InstallDate       : 20060506
InstallDate2      : 20060506000000.000000-000
InstallLocation   :
PackageCache      : C:\WINDOWS\Installer\619ab2.msi
SKUNumber         :
Vendor            : Microsoft Corporation
```

または、**Get-WmiObject Filter** パラメーターを使用して、Microsoft .NET Framework 2.0 のみを選択できます。 このコマンドで使用されているフィルターは WMI フィルターであるため、Windows PowerShell の構文ではなく、WMI クエリ言語 (WQL) 構文を使用します。 代わりに、

```
Get-WmiObject -Class Win32_Product -ComputerName . -Filter "Name='Microsoft .NET Framework 2.0'"| Format-List -Property *
```

WQL クエリは、スペースや等号など、Windows PowerShell で特別な意味を持つ文字を頻繁に使用することに注意してください。 このため、フィルターのパラメーターの値は、常に引用符で囲むのが安全です。 また、読みやすさは向上しませんが、Windows PowerShell のエスケープ文字であるバックティック (`) を使用することもできます。 次のコマンドは前のコマンドと同等で、同様の結果を返しますが、フィルター文字列全体を引用符で囲むのではなく、バックティックを使用して説くス文字をエスケープします。

```
Get-WmiObject -Class Win32_Product -ComputerName . -Filter Name`=`'Microsoft` .NET` Framework` 2.0`' | Format-List -Property *
```

関心のあるプロパティのみの一覧を表示するには、書式設定コマンドレットの Property パラメーターを使用して、必要なプロパティの一覧を表示します。

```
Get-WmiObject -Class Win32_Product -ComputerName . | Format-List -Property Name,InstallDate,InstallLocation,PackageCache,Vendor,Version,IdentifyingNumber
...
Name              : HighMAT Extension to Microsoft Windows XP CD Writing Wizard
InstallDate       : 20051022
InstallLocation   : C:\Program Files\HighMAT CD Writing Wizard\
PackageCache      : C:\WINDOWS\Installer\113b54.msi
Vendor            : Microsoft Corporation
Version           : 1.1.1905.1
IdentifyingNumber : {FCE65C4E-B0E8-4FBD-AD16-EDCBE6CD591F}
...
```

最後に、インストールされたアプリケーションの名前のみを検索するには、単純な **Format-Wide** ステートメントにより、出力が簡略化されます。

```
Get-WmiObject -Class Win32_Product -ComputerName .  | Format-Wide -Column 1
```

これまでインストールに Windows インストーラーを使用したアプリケーションを検索する方法をいくつか確認しましたが、他のアプリケーションはまだ検討していません。 ほとんどの標準的なアプリケーションはアンインストーラーを Windows に登録するため、Windows レジストリでこれらを検索することで、ローカルで操作できます。

### アンインストール可能なすべてのアプリケーションを一覧表示する
システム上のすべてのアプリケーションを検索する確実な方法はありませんが、[プログラム追加と削除] ダイアログ ボックスに表示される一覧ですべてのプログラムを検索することができます。 [プログラム追加と削除] は、次のレジストリ キーでこれらのアプリケーションを検索します。

**HKEY_LOCAL_MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Uninstall**します。

また、このキーを確認してアプリケーションを検索することもできます。 Uninstall キーを簡単に表示できるように、このレジストリの場所に Windows PowerShell ドライブをマップできます。

```
PS>    

Name       Provider      Root                                   CurrentLocation
----       --------      ----                                   ---------------
Uninstall  Registry      HKEY_LOCAL_MACHINE\SOFTWARE\Micr...
```

> [!NOTE]
> **HKLM:** ドライブは、**HKEY_LOCAL_MACHINE** のルートにマップされているので、このドライブを Uninstall キーのパスに使用しました。 **HKLM:** の代わりに、**HKLM** または **HKEY_LOCAL_MACHINE** のいずれかを使用して、レジストリのパスを指定できます。 既存のレジストリ ドライブを使用する利点は、タブ補完を利用してキーの名前を入力できるため、ユーザーが入力する必要がないことです。

これで、"Uninstall" という名前のドライブができ、これを利用することでアプリケーションのインストールの検索が迅速かつ便利になります。 Uninstall: Windows PowerShell ドライブで、レジストリ キーの数をカウントすることによって、インストールされているアプリケーションの数がわかります。

```
PS> (Get-ChildItem -Path Uninstall:).Count
459
```

**Get-ChildItem** をはじめとするさまざまな方法を利用して、アプリケーションの一覧を詳細に検索できます。 アプリケーションの一覧を取得して、**$UninstallableApplications** 変数に保存するには、次のコマンドを使用します。

```
$UninstallableApplications = Get-ChildItem -Path Uninstall:
```

> [!NOTE]
> ここでは、わかりやすくするために長い変数名を使用しています。 実際に使用するときは、長い名前を使用する必要はありません。 変数名にタブ補完を使用できますが、1 ～ 2 文字の名前を使用すると時間を短縮できます。 長くわかりやすい名前は、再利用するためのコードを開発する際に役立ちます。

Uninstall の下のレジストリ キーにレジストリ エントリの値を表示するには、レジストリ キーの GetValue メソッドを使用します。 メソッドの値は、レジストリ エントリの名前です。

たとえば、Uninstall キーの下でアプリケーションの表示名を検索するには、次のコマンドを使用します。

```
PS> Get-ChildItem -Path Uninstall: | ForEach-Object -Process { $_.GetValue("DisplayName") }
```

これらの値が一意である保証はありません。 次の例では、インストールされている 2 つの項目が "Windows Media エンコーダー 9 シリーズ" として表示されます。

```
PS> Get-ChildItem -Path Uninstall: | Where-Object -FilterScript { $_.GetValue("DisplayName") -eq "Windows Media Encoder 9 Series"}

   Hive: Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Micros
oft\Windows\CurrentVersion\Uninstall

SKC  VC Name                           Property
---  -- ----                           --------
  0   3 Windows Media Encoder 9        {DisplayName, DisplayIcon, UninstallS...
  0  24 {E38C00D0-A68B-4318-A8A6-F7... {AuthorizedCDFPrefix, Comments, Conta...
```

### アプリケーションのインストール
**Win32_Product** クラスを使用して、リモートまたはローカルで、Windows インストーラー パッケージをインストールできます。

> [!NOTE]
> Windows Vista、Windows Server 2008、およびそれ以降のバージョンの Windows では、アプリケーションをインストールするには、[管理者として実行] オプションを使用して Windows PowerShell を起動する必要があります。

リモートでインストールする場合は、WMI のサブシステムが Windows PowerShell のパスを認識しないため、.msi パッケージへのパスを指定するのに、汎用名前付け規則 (UNC) のネットワーク パスを使用します。 たとえば、リモート コンピューター PC01 のネットワーク共有 \\AppServ\dsp にある NewPackage.msi パッケージをインストールするには、Windows PowerShell プロンプトで次のコマンドを入力します。

```
(Get-WMIObject -ComputerName PC01 -List | Where-Object -FilterScript {$_.Name -eq "Win32_Product"}).Install(\\AppSrv\dsp\NewPackage.msi)
```

Windows インストーラー テクノロジを使用しないアプリケーションは、展開の自動化に利用できるアプリケーション固有の方法があります。 展開を自動化するための方法があるかどうかを確認するには、アプリケーションのマニュアルを参照するか、アプリケーション ベンダーのサポート システムを参照してください。 場合によっては、アプリケーション ベンダーが特にインストールの自動化に対応するようアプリケーションを設計していなくても、インストーラー ソフトウェアの製造元が自動化のためのテクニックをいくつか持っていることがあります。

### アプリケーションの削除
Windows PowerShell を使用した Windows インストーラー パッケージの削除は、パッケージのインストールとほとんど同じ方法で機能します。 名前に基づいてアンインストールするパッケージを選択する例を以下に示します。場合によっては、**IdentifyingNumber** を使用してフィルター処理した方が簡単になることがあります。

```
(Get-WmiObject -Class Win32_Product -Filter "Name='ILMerge'" -ComputerName . ).Uninstall()
```

他のアプリケーションの削除は、ローカルで実行する場合でも、それほど単純ではありません。 **UninstallString** プロパティを抽出することで、これらのアプリケーションに対するコマンド ラインのアンインストール文字列を検索できます。 このメソッドは、Windows インストーラー アプリケーションや Uninstall キーの下に表示される古いプログラムに対して機能します。

```
Get-ChildItem -Path Uninstall: | ForEach-Object -Process { $_.GetValue("UninstallString") }
```

必要であれば、出力を表示名でフィルター処理できます。

```
Get-ChildItem -Path Uninstall: | Where-Object -FilterScript { $_.GetValue("DisplayName") -like "Win*"} | ForEach-Object -Process { $_.GetValue("UninstallString") }
```

ただし、これらの文字列は、Windows PowerShell プロンプトから直接使用するには、いくつか変更が必要な場合があります。

### Windows インストーラー アプリケーションのアップグレード
アプリケーションをアップグレードするには、アプリケーションの名前、およびアプリケーションのアップグレード パッケージのパスを認識している必要があります。 その情報があれば、1 つの Windows PowerShell コマンドを使用してアプリケーションをアップグレードすることができます。

```
(Get-WmiObject -Class Win32_Product -ComputerName . -Filter "Name='OldAppName'").Upgrade(\\AppSrv\dsp\OldAppUpgrade.msi)
```




<!--HONumber=Oct16_HO1-->


