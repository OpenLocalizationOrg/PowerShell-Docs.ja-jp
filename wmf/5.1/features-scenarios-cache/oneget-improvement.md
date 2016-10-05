---
title: "PackageManagement (別名  OneGet) の機能強化"
contributor: jianyunt, quoctruong
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: bb1129e6aa20b64e94ddb6d7b7cf7b51b1df9ca3

---

>注: わかりやすいタイトルと簡単な説明を提供します

## PackageManagement (別名  OneGet) の機能強化 ##
以下は、WMF 5.0 リリースに見られたユーザー エクスペリエンスのギャップの一部に対処するために WMF 5.1 で行われた修正です。 

1 **バージョン エイリアス**

**シナリオ**: パッケージ P1 のバージョン 1.0 および 2.0 がシステムにインストールされていて、バージョン 1.0 をアンインストールしたいので、"uninstall-package -name P1 -version 1.0" を実行するものとします。 このコマンドを実行すると、バージョン 1.0 がアンインストールされるものと想定していました。 しかし、結果はバージョン 2.0 がアンインストールされます。 
    
この問題の原因は、"-version" パラメーターが "-minimumversion" パラメーターのエイリアスであることです。 OneGet は、最小バージョンが 1.0 という条件を満たすパッケージを探して、最新のバージョンを返します。 ほとんどのユーザーは最新バージョンを探すため、この動作は通常のケースであれば想定されていたものです。 しかし、uninstall-package の場合には当てはまりません。
    
**解決策**: OneGet および PowerShellGet では -version エイリアスが完全に削除されました。 

2 **NuGet プロバイダーのブートストラップに対する複数のプロンプト**

**シナリオ**: Find-Module、install-module、または他の OneGet コマンドレットをコンピューターで初めて実行すると、OneGet は NuGet プロバイダーをブートストラップしようとします。 これは、PowerShellGet プロバイダーは PowerShell モジュールをダウンロードするために NuGet プロバイダーも使用するためです。 そのとき、OneGet は NuGet プロバイダーをインストールする許可をユーザーに求めます。 ユーザーがブートストラップに対して "はい" を選択すると、最新バージョンの NuGet プロバイダーがインストールされます。 
    
ただし、古いバージョンの NuGet プロバイダーがコンピューターにインストールされている場合があり、古いバージョンの NuGet が PowerShell セッションに最初に読み込まれることがあります (OneGet での競合状態)。 ただし、PowerShellGet が動作するには新しいバージョンの NuGet プロバイダーが必要なので、PowerShellGet は OneGet に NuGet プロバイダーのブートストラップを再び要求します。 NuGet プロバイダーのブートストラップに対して複数のプロンプトが表示されるのはこのような理由です。

**解決策**: OneGet は、NuGet プロバイダーのブートストラップに対して複数のプロンプトが表示されるのを避けるため、NuGet プロバイダーの最新バージョンを読み込むようになっています。

回避策も存在します。古いバージョンの NuGet プロバイダー (NuGet-Anycpu.exe) が存在する場合は、$env:ProgramFiles\PackageManagement\ProviderAssemblies または $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies から手動で削除します。


3 **イントラネットのみのコンピューター**

**シナリオ**: エンタープライズのシナリオで、ユーザーはイントラネットのみでインターネット アクセスのない環境で作業しています。 OneGet は WMF 5.0 でこのケースをサポートしていませんでした。

**解決策**:
- インターネットに接続できる別のコンピューターで "Install-PackageProvider -Name NuGet" コマンドを使用して NuGet プロバイダーをダウンロードできます。

- $env:ProgramFiles\PackageManagement\ProviderAssemblies\nuget または $env:LOCALAPPDATA\PackageManagement\ProviderAssemblies\nuget で、インストールした NuGet プロバイダーを見つけます。 

- インターネットにアクセスできないコンピューターでもアクセスできるフォルダーまたはネットワーク共有の場所にバイナリをコピーし、"Install-PackageProvider NuGet -Source <Path to folder>" で NuGet プロバイダーをインストールします。


4 **イベント ログ**

パッケージをインストールすると、コンピューターの状態が変化します。 診断のため、OneGet は install、uninstall、save-package のイベントを Windows イベント ログに記録するようになりました。 イベント チャネルは PowerShell と同じで、Microsoft-Windows-PowerShell, Operational です。

5 **認証のサポート** OneGet は、基本認証を必要とするリポジトリからのパッケージの検索とインストールをサポートするようになりました。 Find-Package および Install-Package コマンドレットに資格情報を渡すことができます。 たとえば、次のように入力します。
``` PowerShell
Find-Package -Source <SourceWithCredential> -Credential (Get-Credential)
```
6 **プロキシの背後での OneGet の使用**

OneGet は、プロキシ パラメーター -ProxyCredential と -Proxy を受け取るようになりました。 これらのパラメーターを使用すると、プロキシの URL と資格情報を OneGet コマンドレットに指定できます (既定では、システムのプロキシ設定が使用されます)。 たとえば、次のように入力します。
``` PowerShell
Find-Package -Source http://www.nuget.org/api/v2/ -Proxy http://www.myproxyserver.com -ProxyCredential (Get-Credential)
```



<!--HONumber=Oct16_HO1-->


