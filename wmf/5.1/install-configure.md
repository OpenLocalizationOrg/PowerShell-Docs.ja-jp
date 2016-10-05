---
title: "WMF 5.1 のインストールと構成 (プレビュー)"
ms.date: 2016-05-16
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
contributor: kriscv
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 30445aecee478d4fceb6cd446f26f00fe2ecc3d8

---

# WMF 5.1 のインストールと構成 (プレビュー) #

## .Net 4.6 のインストール
WMF 5.1. を使用するには、.NET Framework 4.6 をインストールする必要があります。 インストールを行うには、新しいカタログ署名機能を有効にする必要があります。その場合、WMF 5.1 で読み込まれるモジュールとスクリプトの複数の領域が影響を受けます。 

[.NET Framework 4.6 は、サポート技術情報 3045560 として利用できます](https://support.microsoft.com/en-us/kb/3045560)。 インストール手順については、ダウンロード場所で入手できます。

> **注:** .Net 4.6 に関する要件が WMF 5.1 プレビュー インストーラーで検出されないことが既知の問題としてわかっています。このため、.NET 4.6 をインストールする前に WMF 5.1 プレビューをインストールできるようになります。 Microsoft のテストによれば、WMF 5.1 プレビューをインストールした後で、.NET 4.6 をインストールすることはできます。 WMF 5.1 の最終バージョンでは、インストールの前に、この必要条件が満たされているか正確に確認します。 

## WMF 5.1 プレビューのダウンロードとインストール

インストールするオペレーティング システムとアーキテクチャに合わせて WMF 5.1 パッケージをダウンロードします。

| オペレーティング システム       | 前提条件 | パッケージ リンク             |
|------------------------|---------------|---------------------------|
| Windows Server 2012 R2 | [.NET framework 4.6](https://support.microsoft.com/en-us/kb/3045560) | [Win8.1AndW2K12R2 KB3156422-x64.msu](http://go.microsoft.com/fwlink/?LinkID=823586)|
| Windows Server 2012    | [.NET framework 4.6](https://support.microsoft.com/en-us/kb/3045560) | [W2K12 KB3156423-x64.msu](http://go.microsoft.com/fwlink/?LinkID=823587)|
| Windows Server 2008 R2 | [.NET framework 4.6](https://support.microsoft.com/en-us/kb/3045560) </br> [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) </br> [SHA-2 コード署名](https://technet.microsoft.com/en-us/library/security/3033929)用のセキュリティ更新プログラム | [Win7AndW2K8R2 KB3156424-x64.msu](http://go.microsoft.com/fwlink/?LinkID=823588) |
| Windows 8.1            | [.NET framework 4.6](https://support.microsoft.com/en-us/kb/3045560) | **x64:** [Win8.1AndW2K12R2-KB3156422-x64.msu](http://go.microsoft.com/fwlink/?LinkID=823586) </br> **x86:** [Win8.1-KB3156422-x86.msu](http://go.microsoft.com/fwlink/?LinkID=823589) |
| Windows 7 SP1          | [.NET framework 4.6](https://support.microsoft.com/en-us/kb/3045560) </br> [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) </br> [SHA-2 コード署名](https://technet.microsoft.com/en-us/library/security/3033929)用のセキュリティ更新プログラム | **x64:** [Win7AndW2K8R2-KB3156424-x64.msu](http://go.microsoft.com/fwlink/?LinkID=823588) </br> **x86:** [Win7-KB3156424-x86.msu](http://go.microsoft.com/fwlink/?LinkID=823590) |


## Windows エクスプローラー (または Windows Server 2012 R2 または Windows 8.1 ではファイル エクスプローラー) から WMF 5.1 をインストールする

1. MSU ファイルをダウンロードしたフォルダーに移動します。

2. MSU をダブルクリックして実行します。

## コマンド プロンプトから WMF 5.1 をインストールする##

1. コンピューターのアーキテクチャに適切なパッケージをダウンロードした後、管理者特権 (管理者として実行) を使ってコマンド プロンプト ウィンドウを開きます。 Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 の Server Core インストール オプションで、既定で管理者特権でコマンド プロンプトが開きます。

2. WMF 5.1 のインストール パッケージをダウンロードまたはコピーしたフォルダーにディレクトリを変更します。

3. 次のいずれかのコマンドを実行します。
    - Windows Server 2012 R2 または Windows 8.1 x64 を実行しているコンピューターの場合は、`Win8.1AndW2K12R2-KB3156422-x64.msu /quiet` を実行します。
    - Windows Server 2012 を実行しているコンピューターの場合は、`W2K12-KB3156423-x64.msu /quiet` を実行します。
    - Windows Server 2008 R2 SP1 または Windows 7 SP1 x64 を実行しているコンピューターの場合は、`Win7AndW2K8R2-KB3156424-x64.msu /quiet` を実行します。
    - Windows 8.1 x86 を実行しているコンピューターの場合は、`Win8.1-KB3156422-x86.msu /quiet` を実行します。
    - Windows 7 SP1 x86 を実行しているコンピューターの場合は、`Win7-KB3156424-x86.msu /quiet` を実行します。

## Windows Server 2008 SP1 および Windows 7 SP1 でのインストールに関する追加の注記##
Windows Server 2008 SP1 または Windows 7 SP1 に WMF 5.1 をインストールするには、以下をインストールする必要があります。
- 最新のサービス パック。
- [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855)
- WMF 5.1 には、[Microsoft .NET Framework 4.6](https://support.microsoft.com/en-us/kb/3045560) が必要です。 Microsoft .NET Framework 4.6 をインストールするには、ダウンロード場所で提供されている手順に従ってください。
- [SHA-2 コード署名](https://technet.microsoft.com/en-us/library/security/3033929)用のセキュリティ更新プログラム。 このプログラムは、Windows カタログ ファイル用の新しい PowerShell コマンドレットを使用するときに必要です。 

> **WinRM の依存関係** - Windows PowerShell Desired State Configuration (DSC) は、WinRM に依存します。 WinRM は、Windows Server 2008 R2 および Windows 7 では既定で無効になっています。 WinRM を有効にするには、Windows PowerShell 管理者特権セッションで `Set-WSManQuickConfig` を実行します。




<!--HONumber=Oct16_HO1-->


