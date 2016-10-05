---
title: "Windows PowerShell SDK のインストール"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: c3636b45-61aa-4720-85f0-58312c4fc8f9
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 7af27dc9bd8e93d1df5258b0d8df8af12726f568

---

# Windows PowerShell SDK のインストール

以下のトピックでは、異なるバージョンの Windows に PowerShell SDK をインストールする方法について説明します。

## Windows 8 と Windows Server 2012 での Windows PowerShell 3.0 SDK のインストール

Windows PowerShell 3.0 は Windows 8 と Windows Server 2012 で自動的にインストールされます。
さらに、Windows 8 SDK の一部としての Windows PowerShell 3.0 の参照アセンブリもダウンロードおよびインストールできます。
これらのアセンブリでは、Windows PowerShell 3.0 のコマンドレット、プロバイダー、およびホスト プログラムを記述できます。
Windows 8 用 Windows SDK をインストールすると、Windows PowerShell アセンブリが自動的に \Program Files (x86)\Reference Assemblies\Microsoft\WindowsPowerShell\3.0 にインストールされます。
詳細については、[Windows 8 SDK ダウンロード サイト](http://msdn.microsoft.com/windows/hardware/hh852363.aspx)を参照してください。
Windows PowerShell のサンプル コードもデベロッパー センターで入手できます。
詳細については、[デベロッパー センター サイト](http://code.msdn.microsoft.com/windowsdesktop/)のデスクトップ サンプル コード ページをご覧ください。

さらに、Windows PowerShell 3.0 には Windows PowerShell 2.0 SDK との下位互換性があり、これには多くのサンプルコードが含まれます。
Windows PowerShell 2.0 SDK をダウンロードする方法の詳細については、以下をご覧ください。
(2.0 のサンプル コードは Windows 8 および Windows PowerShell 3.0 と互換性がありますが、Windows PowerShell 2.0 を Windows 8 プラットフォームにインストールすることはできません)。

##Windows 7 と Windows Server 2008 R2 での Windows PowerShell 3.0 SDK のインストール

Windows 7 と Windows Server 2008 R2 では、PowerShell 2.0 が自動的にインストールされます。
さらに、これらのシステムに PowerShell 3.0 をインストールできます。
(詳細については、「[Windows PowerShell のインストール](Installing-Windows-PowerShell.md)」をご覧ください。)
前述のように、Windows 7 および Windows Server 2008 R2 で Windows 8 SDK をインストールすることもできます。

## Windows 7、Vista、XP、Server 2003、Server 2008 での Windows PowerShell 2.0 SDK のインストール

Windows PowerShell 2.0 SDK は、コマンドレット、プロバイダー、アプリケーションのホストを記述するために必要な参照アセンブリを提供し、コードの記述を開始するときに開始点として使用できる C# サンプル コードを提供します。

この SDK をインストールするには、「[Windows PowerShell 2.0 SDK](http://go.microsoft.com/fwlink/?LinkId=184611)」を参照してください。

## 参照アセンブリ

既定では、参照アセンブリは `c:\Program Files\Reference Assemblies\Microsoft\WindowsPowerShell\V1.0` にインストールされます。

> **注記**: Windows PowerShell 2.0 のアセンブリに対してコンパイルされるコードは、Windows PowerShell 1.0 のインストールに読み込むことができません。
>ただし、Windows PowerShell 1.0 のアセンブリに対してコンパイルされるコードは、Windows PowerShell 2.0 のインストールに読み込むことができます。

## サンプル

既定では、コード サンプルは `C:\Program Files\Microsoft SDKs\Windows\v7.0\Samples\sysmgmt\WindowsPowerShell\` にインストールされます。

次のセクションでは、各サンプル コードについて簡単に説明します。

## コマンドレット サンプル
**GetProcessSample01**

ローカル コンピューターのすべてのプロセスを取得する簡単なコマンドレットを記述する方法を示します。

**GetProcessSample02**

コマンドレットにパラメーターを追加する方法を示します。
このコマンドレットは 1 つまたは複数のプロセス名を取得し、それに一致するプロセスを返します。

**GetProcessSample03**

パイプラインからの入力を受け入れるパラメーターを追加する方法を示します。

**GetProcessSample04**

終了しないエラーの処理方法を示します。

**GetProcessSample05**

指定されたプロセスの一覧を表示する方法を示します。

**SelectObject**

特定のオブジェクトのみを選択するフィルターの記述方法を示します。

**SelectString**

指定されたパターンのファイルを検索する方法を示します。

**StopProcessSample01**

*PassThru* パラメーターの実装方法と、[ShouldProcess](https://technet.microsoft.com/library/system.management.automation.cmdlet.shouldprocess.aspx) メソッドと [ShouldContinue](https://technet.microsoft.com/library/system.management.automation.cmdlet.shouldcontinue.aspx) メソッドを呼び出すことでユーザーにフィードバックを要求する方法を示します。
ユーザーはオブジェクトを返すようにコマンドレットに強制するとき、*PassThru* を指定します。

**StopProcessSample02**

特定のプロセスの停止方法を示します。

**StopProcessSample03**

パラメーターのエイリアスの宣言方法とワイルドカードのサポート方法を示します。

**StopProcessSample04**

パラメーター セットの宣言方法、コマンドレットが入力として取得するオブジェクト、既定として使用するパラメーター セットの指定方法を示します。

## リモート処理のサンプル

**RemoteRunspace01**

リモート接続の確立に使用されるリモート実行空間の作成方法を示します。

**RemoteRunspacePool01**

リモート実行空間プールの構築方法とそのプールを使用して複数のコマンドを同時に実行する方法を示します。

**Serialization01**

既存の .NET クラスを見て、そのクラスで選択されているパブリック プロパティからの情報がシリアル化/逆シリアル化全体で維持されていることを確認する方法を示します。

**Serialization02**

既存の .NET クラスを見て、そのクラスのインスタンスからの情報がクラスのパブリック プロパティで利用できないとき、その情報がシリアル化/シリアル化解除全体で維持されていることを確認する方法を示します。

**Serialization03**

既存の .NET クラスを見て、そのクラスのインスタンスと派生クラスのインスタンスがライブ .NET オブジェクトに逆シリアル化 (リハイドレート) されていることを確認する方法を示します。

## イベントのサンプル

**Event01**

ObjectEventRegistrationBase から派生させることでイベント登録のコマンドレットを作成する方法を示します。

**Event02**

リモート コンピューターで生成された Windows PowerShell イベントの通知を受信する方法を示します。
[Runspace](https://technet.microsoft.com/library/system.management.automation.runspaces.runspace.aspx) クラスで公開された PSEventReceived イベントを使用します。

## アプリケーションのホストのサンプル

**Runspace01**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) クラスを使用し、[Get-Process](http://go.microsoft.com/fwlink/?LinkId=113324) コマンドレットを同期的に実行する方法を示します。
[Get-Process](http://go.microsoft.com/fwlink/?LinkId=113324) コマンドレットは、ローカル コンピューターで実行されているプロセスごとに [Process](https://technet.microsoft.com/library/system.diagnostics.process.aspx) オブジェクトを返します。

**Runspace02**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) クラスを使用し、[Get-Process](http://go.microsoft.com/fwlink/?LinkId=113324) コマンドレットと [Sort-Object](http://go.microsoft.com/fwlink/?LinkID=113403) コマンドレットを同期的に実行する方法を示します。
[Get-Process](http://go.microsoft.com/fwlink/?LinkId=113324) コマンドレットは、ローカル コンピューターで実行されているプロセスごとに [Process](https://technet.microsoft.com/library/system.diagnostics.process.aspx) オブジェクトを返します。Sort-Object は、[Id](https://technet.microsoft.com/library/system.diagnostics.process.id.aspx) プロパティに基づいてオブジェクトを並べ替えます。
これらのコマンドの結果は、[DataGridView](https://technet.microsoft.com/library/system.windows.forms.datagridview.aspx) コントロールを利用して表示されます。

**Runspace03**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) クラスを使用してスクリプトを同期的に実行する方法と終了しないエラーを処理する方法を示します。
このスクリプトはプロセス名の一覧を受信し、これらのプロセスを取得します。
スクリプトの実行時に生成された終了しないエラーを含む、スクリプトの結果がコンソール ウィンドウに表示されます。

**Runspace04**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) クラスを使用してコマンドを実行する方法とコマンドの実行時にスローされた終了するエラーをキャッチする方法を示します。
2 つのコマンドが実行され、最後のコマンドには無効なパラメーターの引数が渡されます。
結果として、オブジェクトは返されず、終了するエラーがスローされます。

**Runspace05**

実行空間が開いたとき、スナップショットのコマンドレットが利用できるように [InitialSessionState](https://technet.microsoft.com/library/system.management.automation.runspaces.initialsessionstate.aspx) オブジェクトにスナップインを追加する方法を示します。
このスナップインにより、[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトを使用して同期的に実行される Get-Proc コマンドレット ([GetProcessSample01 サンプル](https://technet.microsoft.com/library/ff602028.aspx)により定義) が与えられます。

**Runspace06**

実行空間が開いたとき、モジュールが読み込まれるように [InitialSessionState](https://technet.microsoft.com/library/system.management.automation.runspaces.initialsessionstate.aspx) オブジェクトにモジュールを追加する方法を示します。
このモジュールにより、[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトを使用して同期的に実行される Get-Proc コマンドレット ([GetProcessSample02 サンプル](https://technet.microsoft.com/library/ff602027.aspx)により定義) が与えられます。

**Runspace07**

実行空間を作成し、その実行空間を使用し、[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトを使用して 2 つのコマンドレットを同期的に実行する方法を示します。

**Runspace08**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトのパイプラインにコマンドと引数を追加する方法とコマンドを同期的に実行する方法を示します。

**Runspace09**

[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトのパイプラインにスクリプトを追加する方法とそのスクリプトを非同期的に実行する方法を示します。
イベントはスクリプトの出力を処理するために使用されます。

**Runspace10**

既定の最初のセッション状態を作成する方法、コマンドレットを [InitialSessionState](https://technet.microsoft.com/library/system.management.automation.runspaces.initialsessionstate.aspx) に追加する方法、最初のセッション状態を使用する実行空間を作成する方法、[PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) オブジェクトを使用してコマンドを実行する方法を示します。

**Runspace11**

[ProxyCommand](https://technet.microsoft.com/library/system.management.automation.proxycommand.aspx) クラスを使用し、既存のコマンドレットを呼び出すが、利用できるパラメーターのセットを制約するプロキシ コマンドを作成する方法を示します。
このプロキシ コマンドは、制約付き実行空間の作成に使用される最初のセッション状態に追加されます。
つまり、ユーザーはプロキシ コマンドを使用しないとコマンドレットの機能にアクセスできません。

**PowerShell01**

[InitialSessionState](https://technet.microsoft.com/library/system.management.automation.runspaces.initialsessionstate.aspx) オブジェクトを使用し、制約のある実行空間を作成する方法を示します。

**PowerShell02**

実行空間プールを使用し、複数のコマンドを同時に実行する方法を示します。

## ホストのサンプル

**Host01 という**

カスタム ホストを使用するホスト アプリケーションを実装する方法を示します。
このサンプルでは、カスタム ホストを使用する実行空間が作成され、その後 [PowerShell](https://technet.microsoft.com/library/system.management.automation.powershell.aspx) API を使用して "exit" を呼び出すスクリプトが実行されます。
ホスト アプリケーションはスクリプトの出力を確認し、結果を印刷します。

**Host02**

カスタム ホストの実装と共に Windows PowerShell ランタイムを使用するホスト アプリケーションを記述する方法を示します。
ホスト アプリケーションはホストのカルチャをドイツ語に設定し、[Get-Process](http://go.microsoft.com/fwlink/?LinkId=113324) コマンドレットを実行して、pwrsh.exe を使用して確認できるものと同じ結果を表示し、最新のデータと時刻をドイツ語で印刷します。

**Host03**

コマンドラインからコマンドを読み取ってコマンドを実行し、結果をコンソールに表示する対話型コンソール ベースのホスト アプリケーションを構築する方法を示します。

**Host04**

コマンドラインからコマンドを読み取ってコマンドを実行し、結果をコンソールに表示する対話型コンソール ベースのホスト アプリケーションを構築する方法を示します。
このホスト アプリケーションでは、複数選択を指定するための確認メッセージを表示することもできます。

**Host05**

コマンドラインからコマンドを読み取ってコマンドを実行し、結果をコンソールに表示する対話型コンソール ベースのホスト アプリケーションを構築する方法を示します。
このホスト アプリケーションでは、[Enter-PsSession](http://go.microsoft.com/fwlink/?LinkId=135210) コマンドレットと [Exit-PsSession](http://go.microsoft.com/fwlink/?LinkId=135212) コマンドレットを使用してリモート コンピューターを呼び出すこともできます。

**Host06**

コマンドラインからコマンドを読み取ってコマンドを実行し、結果をコンソールに表示する対話型コンソール ベースのホスト アプリケーションを構築する方法を示します。
さらに、このサンプルでは、トークナイザー API を使用して、ユーザーが入力したテキストの色を指定します。

## プロバイダーのサンプル

**AccessDBProviderSample01**

[CmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.cmdletprovider.aspx) クラスから直接派生するプロバイダー クラスの宣言方法を示します。
すべてを網羅しておく目的でここに含めておきます。

**AccessDBProviderSample02**

New-PSDrive コマンドレットと Remove-PSDrive コマンドレットを呼び出すために [NewDrive](https://technet.microsoft.com/library/system.management.automation.provider.drivecmdletprovider.newdrive.aspx) メソッドと [RemoveDrive](https://technet.microsoft.com/library/system.management.automation.provider.drivecmdletprovider.removedrive.aspx) メソッドを上書きする方法を示します。
このサンプルのプロバイダー クラスは [DriveCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.drivecmdletprovider.aspx) クラスから派生します。

**AccessDBProviderSample03**

Get-Item コマンドレットと Set-Item コマンドレットを呼び出すために [GetItem](https://technet.microsoft.com/library/system.management.automation.provider.itemcmdletprovider.getitem.aspx) メソッドと [SetItem](https://technet.microsoft.com/library/system.management.automation.provider.itemcmdletprovider.setitem.aspx) メソッドを上書きする方法を示します。
このサンプルのプロバイダー クラスは [ItemCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.itemcmdletprovider.aspx) クラスから派生します。

**AccessDBProviderSample04**

Copy-Item コマンドレット、Get-ChildItem コマンドレット、New-Item コマンドレット、Remove-Item コマンドレットを呼び出すためにコンテナー メソッドを上書きする方法を示します。
これらのメソッドは、データ ストアにコンテナーであるアイテムが含まれる場合に実装する必要があります。
コンテナーは、共通の親項目の子項目のグループです。
このサンプルのプロバイダー クラスは [ItemCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.itemcmdletprovider.aspx) クラスから派生します。

**AccessDBProviderSample05**

Move-Item コマンドレットと Join-Path コマンドレットを呼び出すためにコンテナー メソッドを上書きする方法を示します。
これらのメソッドは、ユーザーがコンテナー内で項目を移動する必要があり、データ ストアに入れ子状態のコンテナーが含まれる場合に実装する必要があります。
このサンプルのプロバイダー クラスは [NavigationCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.navigationcmdletprovider.aspx) クラスから派生します。

**AccessDBProviderSample06**

Clear-Content コマンドレット、Get-Content コマンドレット、Set-Content コマンドレットを呼び出すためにコンテナー メソッドを上書きする方法を示します。
これらのメソッドは、ユーザーがデータ ストア内の項目のコンテンツを管理する必要がある場合に実装する必要があります。
このサンプルのプロバイダー クラスは [NavigationCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.navigationcmdletprovider.aspx) クラスから派生し、[IContentCmdletProvider](https://technet.microsoft.com/library/system.management.automation.provider.icontentcmdletprovider.aspx) インターフェイスを実装します。



<!--HONumber=Oct16_HO1-->


