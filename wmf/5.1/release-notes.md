---
title: "WMF 5.1 リリース ノート (プレビュー)"
ms.date: 2016-07-27
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: jkeithb
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 83061e651b190bab3e5914bb6270a5857f7aa7a5

---

# Windows Management Framework (WMF) 5.1 プレビューのリリース ノート #

WMF 5.1 プレビューには、Windows Server 2016 でリリースされる PowerShell、WMI、WinRM、ソフトウェア インベントリおよびライセンス (SIL) のコンポーネントが含まれています。 WMF 5.1 は、Windows 7、Windows 8.1、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 にインストールすることができ、WMF 5.0 RTM と比較すると次のような機能が強化されています。

- 新しいコマンドレット: ローカル ユーザーとグループ、Get-ComputerInfo
- PowerShellGet では、署名付きモジュールの適用や JEA モジュールのインストールなどの機能強化が行われています。
- PackageManagement では、コンテナー、CBS セットアップ、EXE ベースのセットアップ、CAB パッケージをサポートするようになりました。
- DSC および PowerShell クラスにおけるデバッグ機能の強化
- セキュリティの機能強化としては、プル サーバーからもたらされるカタログ署名付きモジュールの適用、PowerShellGet コマンドレットを使用するタイミングなどが挙げられます。
- さまざまなユーザー要求と問題への対応

**重要な注意事項:**

- **WMF 5.1 プレビューには .NET Framework 4.6 が必要です**。 .NET 4.6 がインストールされていない場合、インストールは成功しますが、主な機能は失敗します。 手順については、トピック「[WMF 5.1 のインストールと構成 (プレビュー)](https://msdn.microsoft.com/en-us/powershell/wmf/5.1/install-configure)」を参照してください。 
- 現時点で、**WMF 5.1 プレビューは、運用環境での展開がサポートされていません**。 このプレビューの目的は、リリースに組み込まれる機能について事前情報を紹介し、PowerShell チームにフィードバックする機会をユーザーに提供することにあります。
- WMF 5.1 プレビューは、WMF 5.0 経由で直接インストールしてもかまいません。
- 既知の問題として、Windows 7 および Windows Server 2008 に WMF 5.1 プレビューをインストールするには、現時点では WMF 4.0 が必要です。 この要件は、最終リリースの発行前に削除する予定です。
- WMF 5.1 の将来のバージョン (RTM バージョンを含む) をインストールする場合は、WMF 5.1 プレビューをアンインストールする必要があります。




<!--HONumber=Oct16_HO1-->


