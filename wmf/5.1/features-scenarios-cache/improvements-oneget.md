---
title: "WMF 5.1 の OneGet 機能強化 (プレビュー)"
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
ms.openlocfilehash: 1d0bd545b52ef56045f2ec740b05c4e0fd93bb67

---

# OneGet の機能強化
WMF 5.1 には、WMF 5.0 リリースにあったユーザー エクスペリエンスのギャップの一部に対処するための、いくつかの修正と機能強化が含まれます。 

##バージョン エイリアスの削除

**シナリオ**: パッケージ P1 のバージョン 1.0 および 2.0 がシステムにインストールされていて、バージョン 1.0 をアンインストールしたい場合、"uninstall-package -name P1 -version 1.0" を実行し、このコマンドを実行するとバージョン 1.0 がアンインストールされるものと期待します。 しかし、結果はバージョン 2.0 がアンインストールされます。 
    
このようになるのは、"-version" パラメーターが "-minimumversion" パラメーターのエイリアスであるためです。 OneGet は、最小バージョンが 1.0 という条件を満たすパッケージを探して、最新のバージョンを返します。 たいていの場合は最新バージョンを探すのが目的の結果なので、この動作は通常であれば想定したものです。 しかし、uninstall-package の場合には当てはまりません。
    
**解決策**: WMF 5.1 では、OneGet および PowerShellGet から -version エイリアスが完全に削除されています。 

##NuGet プロバイダーのブートストラップに対する複数のプロンプト

**シナリオ**: Find-Module、Install-module、または他の OneGet コマンドレットをコンピューターで初めて実行すると、OneGet は NuGet プロバイダーをブートストラップしようとします。 これは、PowerShellGet プロバイダーは PowerShell モジュールをダウンロードするために NuGet プロバイダーも使用するためです。 そのとき、OneGet は NuGet プロバイダーをインストールする許可をユーザーに求めます。 ユーザーがブートストラップに対して "はい" を選択すると、最新バージョンの NuGet プロバイダーがインストールされます。 
    
ただし、古いバージョンの NuGet プロバイダーがコンピューターにインストールされている場合があり、古いバージョンの NuGet が PowerShell セッションに最初に読み込まれることがあります (OneGet での競合状態)。 ただし、PowerShellGet が動作するには新しいバージョンの NuGet プロバイダーが必要なので、PowerShellGet は OneGet に NuGet プロバイダーのブートストラップを再び要求します。 これにより、NuGet プロバイダーのブートストラップで複数のプロンプトが表示されます。

**解決策**: WMF 5.1 では、OneGet は、NuGet プロバイダーのブートストラップに対して複数のプロンプトが表示されるのを避けるため、NuGet プロバイダーの最新バージョンを読み込むようになっています。

この問題を回避策することもできます。古いバージョンの NuGet プロバイダー (NuGet-Anycpu.exe) が存在する場合は、$env:ProgramFiles\PackageManagement\ProviderAssemblies または $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies から手動で削除します。


##イントラネット アクセスのみのコンピューターでの OneGet のサポート

**シナリオ**: WMF 5.0 の OneGet は、イントラネット アクセスしかない (インターネットにアクセスできない) コンピューターをサポートしませんでした。

**解決策**: WMF 5.1 では、以下のようにして、イントラネット接続のみのコンピューターで OneGet を使用できます。

1. インターネットに接続できる別のコンピューターで、Install-PackageProvider NuGet を使用して、NuGet プロバイダーをダウンロードします。

2. $env:ProgramFiles\PackageManagement\ProviderAssemblies\nuget または $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies\nuget で NuGet プロバイダーを探します。 

3. イントラネット コンピューターがアクセスできるフォルダーまたはネットワーク共有の場所にバイナリをコピーし、"Install-PackageProvider NuGet -Source <Path to folder>" を使用して NuGet プロバイダーをインストールします。


##イベント ログの機能強化

パッケージをインストールすると、コンピューターの状態が変化します。 WMF 5.1 の OneGet は、install、uninstall、save-package アクティビティのイベントを Windows イベント ログに記録するようになりました。 イベント チャネルは PowerShell と同じで、Microsoft-Windows-PowerShell, Operational です。

##基本認証のサポート

WMF 5.1 の OneGet は、基本認証を必要とするリポジトリからのパッケージの検索とインストールをサポートします。 Find-Package および Install-Package コマンドレットに資格情報を渡すことができます。 たとえば、次のように入力します。

``` PowerShell
Find-Package -Source <SourceWithCredential> -Credential (Get-Credential)
```
##プロキシの背後での OneGet の使用のサポート

WMF 5.1 の OneGet は、新しいプロキシ パラメーター -ProxyCredential と -Proxy を受け取るようになりました。 これらのパラメーターを使用すると、プロキシの URL と資格情報を OneGet コマンドレットに対して指定できます。 既定では、システムのプロキシ設定が使用されます。 たとえば、次のように入力します。

``` PowerShell
Find-Package -Source http://www.nuget.org/api/v2/ -Proxy http://www.myproxyserver.com -ProxyCredential (Get-Credential)
```



<!--HONumber=Oct16_HO1-->


