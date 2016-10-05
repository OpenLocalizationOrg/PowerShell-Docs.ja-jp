---
title: "WMF 5.1 (プレビュー) の新しいシナリオと機能"
ms.date: 2016-07-13
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: keithb
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 9611a7da48a849b52821ac2890e1ea60441a75e3

---

# WMF 5.1 (プレビュー) の新しいシナリオと機能 #

> 注意: この情報は暫定版であり、変更することがあります。

## PowerShell のエディション ##
バージョン 5.1 から、PowerShell はさまざまな機能セットとプラットフォーム互換性を備える別のエディションで使用できます。

- **デスクトップ エディション:** .NET Framework 上に構築されており、Server Core や Windows Desktop などの Windows の完全エディションで実行する PowerShell のバージョンを対象とするスクリプトおよびモジュールとの互換性を提供します。
- **コア エディション:** .NET Core 上に構築されており、Nano Server や Windows IoT などの Windows の縮小エディションで実行する PowerShell のバージョンを対象とするスクリプトおよびモジュールとの互換性を提供します。

**PowerShell のエディションの使用に関する詳細します。**
- [PowerShell の実行中のエディションかを調べます]()
- [特定のバージョンの PowerShell モジュールの互換性を宣言します。]()
- [CompatiblePSEditions で Get モジュールの結果をフィルター処理します。]()
- [PowerShell の互換性のあるエディションで実行しない限り、スクリプトの実行を防ぐ]()

## カタログ コマンドレット  

[Microsoft.PowerShell.Security](https://technet.microsoft.com/en-us/library/hh847877.aspx) モジュールに新しいコマンドレットが 2 つ追加されました。Windows カタログ ファイルを生成し、検証するコマンドレットです。  

###New-FileCatalog 
--------------------------------

New-FileCatalog は、一連のフォルダーやファイルに対して Windows カタログ ファイルを作成します。 このカタログ ファイルには、指定されたパスのすべてのファイルのハッシュが含まれています。 ユーザーは一連のフォルダーと共に、それらのフォルダーを表すカタログ ファイルを配信できます。 カタログ作成時刻以降、フォルダーに変更が加えられたかどうかを検証するとき、この情報が役立ちます。    

```PowerShell
New-FileCatalog [-CatalogFilePath] <string> [[-Path] <string[]>] [-CatalogVersion <int>] [-WhatIf] [-Confirm] [<CommonParameters>]
```
カタログ バージョン 1 と 2 がサポートされています。 バージョン 1 は SHA1 ハッシュ アルゴリズムを使用して、バージョン 2 は SHA256 ハッシュ アルゴリズムを使用してファイル ハッシュを作成します。 *Windows Server 2008 R2* と *Windows 7* はカタログ バージョン 2 に対応していません。 カタログ バージョン 2 は *Windows 8*、*Windows Server 2012* 以降のオペレーティング システムで利用する必要があります。  

![](../images/NewFileCatalog.jpg)

これはカタログ ファイルを作成します。 

![](../images/CatalogFile1.jpg)  

![](../images/CatalogFile2.jpg) 

カタログ ファイルの整合性を検証するために (上記の例では Pester.cat)、[Set-AuthenticodeSignature](https://technet.microsoft.com/library/hh849819.aspx) コマンドレットで署名します。   


###Test-FileCatalog 
--------------------------------

Test-FileCatalog は、一連のフォルダーを表すカタログを検証します。 

```PowerShell
Test-FileCatalog [-CatalogFilePath] <string> [[-Path] <string[]>] [-Detailed] [-FilesToSkip <string[]>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

![](../images/TestFileCatalog.jpg)

このコマンドレットは、*カタログ*で見つかったすべてのファイル ハッシュとその相対パスを*ディスク*のそれらと比較します。 ファイル ハッシュとパスの間に不一致が検出された場合、*ValidationFailed* というステータスを返します。 *-Detailed* パラメーターを利用し、この情報をすべて取得できます。 *署名*プロパティには、カタログの署名ステータスも表示されます。これは、カタログ ファイルで [Get-AuthenticodeSignature](https://technet.microsoft.com/en-us/library/hh849805.aspx) コマンドレットを呼び出すことと同じです。 *-FilesToSkip* パラメーターを利用し、検証中にファイルをスキップすることもできます。 


## モジュール分析キャッシュ ##
WMF 5.1 以降の PowerShell では、エクスポートするコマンドなど、モジュールに関するデータのキャッシュに使用されるファイルを制御できます。

既定では、このキャッシュは `${env:LOCALAPPDATA}\Microsoft\Windows\PowerShell\ModuleAnalysisCache` ファイルに格納されます。
キャッシュは、通常、起動時にコマンドを検索するときに読み取られ、モジュールのインポート後しばらくしてバックグラウンド スレッドで書き込まれます。

キャッシュの既定の場所を変更するには、PowerShell を開始する前に、環境変数 `$env:PSModuleAnalysisCachePath` を設定します。 この環境変数の変更は、子プロセスのみに影響します。 値には、PowerShell がファイルの作成および書き込みアクセス許可を持つ完全なパス (ファイル名を含む) を指定する必要があります。 ファイル キャッシュを無効にするには、たとえば次のような無効な場所をこの値に設定します。

```PowerShell
$env:PSModuleAnalysisCachePath = 'nul'
```

これは、パスを無効なデバイスに設定します。 PowerShell がパスに書き込めない場合、エラーは返されませんが、トレーサーでエラー レポートを見ることができます。

```PowerShell
Trace-Command -PSHost -Name Modules -Expression { Import-Module Microsoft.PowerShell.Management -Force }
```

キャッシュの書き込み時、PowerShell は存在しなくなったモジュールを確認することで、キャッシュが不必要に大きくなるのを防ぎます。
このような確認が望ましくないことがあり、その場合は無効に設定できます。

```PowerShell
$env:PSDisableModuleAnalysisCacheCleanup = 1
```

この環境変数の設定は、現在のプロセスで直ちに有効になります。

##モジュールのバージョンの指定

WMF 5.1 では、`using module` は PowerShell の他のモジュール関連構造と同様に動作します。 以前は、モジュールの特定のバージョンを指定する方法はありませんでした。複数のバージョンが存在する場合、エラーが発生しました。


WMF 5.1 では次のようになります。

* `ModuleSpecification` [ハッシュ テーブル](https://msdn.microsoft.com/en-us/library/jj136290(v=vs.85).aspx)を使用できます。 このハッシュ テーブルの形式は `Get-Module -FullyQualifiedName` と同じです。

**例:** `using module @{ModuleName = 'PSReadLine'; RequiredVersion = '1.1'}`

* モジュールに複数のバージョンがある場合、PowerShell は**同じ解決ロジック**を `Import-Module` として使用し、エラーを返しません。`Import-Module` および `Import-DscResource` と同じ動作です。


##Pester の機能強化
WMF 5.1 では、PowerShell で出荷される Pester のバージョンが 3.3.5 から 3.4.0 に更新され、コミット https://github.com/pester/Pester/pull/484/commits/3854ae8a1f215b39697ac6c2607baf42257b102e が追加されました。Nano Server での Pester の動作を改善します。 

https://github.com/pester/Pester/blob/master/CHANGELOG.md で ChangeLog.md ファイルを調べると、バージョン 3.3.5 から 3.4.0 への変更を確認できます。



<!--HONumber=Oct16_HO1-->


