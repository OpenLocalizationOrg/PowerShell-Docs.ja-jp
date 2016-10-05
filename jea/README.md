---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: README
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 7bb5635832e912b39ec387e8ac93ada24a434ff8

---

# Just Enough Administration
Just Enough Administration (JEA) は、PowerShell で管理できるものすべてに対する委任された管理を有効にするセキュリティ テクノロジです。
JEA を使用すると、以下のことを実行できます。
- **お使いのコンピューター上の管理者の数を減らします**。このためには、通常のユーザーに代わって特権の必要な操作を実行する仮想アカウントを活用します。
- **ユーザーが実行できる操作を制限します**。ここでは、ユーザーが実行できるコマンドレット、関数、および外部コマンドを指定できます。
- "覗き見" トランスクリプションを使用して、**ユーザーの操作を把握します**。このトランスクリプションは、ユーザーがセッション中にどんなコマンドを実行したかを正確に示します。

**なぜこれが重要でしょうか。**  
DNS サーバーが Active Directory ドメイン コントローラーと併置された一般的なシナリオについて考えてみます。
DNS 管理者は、DNS サーバーでの問題を解決するためにローカル管理者特権が必要ですが、そのためには、これらの管理者を高い特権を持つ "Domain Admins" セキュリティ グループのメンバーにする必要があります。
この方法は、ドメイン全体への制御や、そのコンピューター上のすべてのリソースへのアクセス権を DNS 管理者に効果的に付与します。

JEA を配置し、DNS 管理者にロールを構成して、これらの管理者が作業を行うのに必要なすべてのコマンドにアクセスできるように、またそれ以外のことを実行できないように指定できます。
つまり、DNS 管理者に Active Directory への権限を付与しなくても、侵害された DNS キャッシュを修復したり、ファイル システムを参照したり、潜在的に危険性なスクリプトを実行したりできる適切なアクセスを提供できます。
さらに、JEA セッションが 1 回限りの特権仮想アカウントを使用するように構成されている場合、DNS 管理者は*特権のない*資格情報を使用してサーバーに接続できる一方で、特権のあるコマンドを実行することもできます。

## 可用性
JEA は今後の Windows Server 2016 と並行して開発中であり、Windows Management Framework 更新プログラムを通じて以前のバージョンの Windows で使用することができます。
JEA の現在のリリースは、次のプラットフォームで使用できます。

**Windows Server**
- Windows Server 2016 Technical Preview 4 以降
- Windows Server 2012 R2、2012、および 2008 r2 \ * [Windows Management Framework 5.0](https://www.microsoft.com/en-us/download/details.aspx?id=50395) インストール

**Windows クライアント**
- 11 月の更新プログラム (1511) がインストールされた Windows 10
- Windows 8.1、8、および 7 \ * と [Windows Management Framework 5.0](https://www.microsoft.com/en-us/download/details.aspx?id=50395) インストール

\ * JEA セッションの仮想アカウントのサポートは現在 Windows Server 2008 R2 または Windows 7 で利用可能です。


## エクスペリエンス ガイドの利用
ここでは、独自の JEA エンドポイントを作成、展開、使用する方法について説明します。

このガイドでは、事前構築された JEA エンドポイントを使用してエンドユーザー エクスペリエンスとはどのようなものかを知ることですばやく作業を開始できます。また、セッション構成やロール機能などの概念の実証に役立つように、エンドポイントを最初から再作成する手順について説明します。

1.  [概要](introduction.md)   
JEA を検討する必要がある理由を簡単に説明します

2.  [前提条件](prerequisites.md)  
環境の設定方法を説明します

3.  [JEA を使用します。](using-jea.md)  
JEA を使用するオペレーターのエクスペリエンスを理解することから始めます

4.  [デモを再作成します。](remake-the-demo-endpoint.md)  
JEA セッション構成を最初から作成します

5.  [ロールの機能](role-capabilities.md)  
ロール機能ファイルを使用して JEA の機能をカスタマイズする方法について説明します

6.  [エンド ツー エンドの Active Directory](end-to-end---active-directory.md)  
Active Directory を管理するためのまったく新しいエンドポイントを作成します

7.  [複数のコンピューターの展開と保守](multi-machine-deployment-and-maintenance.md)  
規模によって展開と作成がどのように変化するかを理解します

8.  [JEA をレポートします。](reporting-on-jea.md)  
JEA のすべての操作とインフラストラクチャの監査とレポート方法を理解します

9.  付録
  - [このガイド内で使用される主要な概念](key-concepts-used-throughout-this-guide.md)  
  -  [ドメイン コント ローラーの作成](creating-a-domain-controller.md)  
  -  [ブラック リストに](on-blacklisting.md)  
  -  [コマンドを制限する際の考慮事項](considerations-when-limiting-commands.md)  
  -  [ロールの機能のよくある落とし穴](common-role-capability-pitfalls.md)

## 独自の JEA エンドポイントの作成を開始する
JEA エンドポイントの作成は簡単です。必要なのは、JEA 対応のシステムとテキスト エディター (PowerShell ISE など) だけです。
作業開始に役立つヒントは、[`New-PSRoleCapabilityFile -Path <path>`](https://technet.microsoft.com/library/mt631422.aspx) および [`New-PSSessionConfigurationFile -Path <Path>`](https://technet.microsoft.com/library/mt631422.aspx) を使用して、他の引数は使用せずにスケルトン ファイルを作成することです。
これらのスケルトン ファイルには、すべての適用可能な構成フィールドと、各フィールドの使用目的を説明するコメントが含まれています。

JEA エンドポイントのさらに簡単な作成方法については、[JEA ツールキット ヘルパー](http://blogs.technet.com/b/privatecloud/archive/2015/12/20/introducing-the-updated-jea-helper-tool.aspx)を参照してください。ここに用意された GUI を使用して、セッション構成およびロール機能ファイルを作成できます。
また、PowerShell ログに基づいたロール機能の生成もサポートされており、ユーザーが業務で通常実行するコマンドで作業を開始することもできます。




<!--HONumber=Oct16_HO1-->


