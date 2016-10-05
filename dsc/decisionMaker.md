---
title: "意思決定者向け Desired State Configuration の概要"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 2d2b142dc862f7655f28aa34e1fd91f63bd6286e

---

# 意思決定者向け Desired State Configuration の概要 #

このドキュメントでは、PowerShell Desired State Configuration (DSC) を使用するビジネス上の利点について説明します。 テクニカル ガイドではありません。

## Desired State Configuration とは ##

Windows PowerShell Desired State Configuration (DSC) は、オープン スタンダードに基づく、Windows に組み込まれた構成管理プラットフォームです。 DSC は、展開ライフサイクル (開発、テスト、実稼働前、実稼働) の各段階、およびスケールアウト中に、機能の信頼性と一貫性を維持するために十分な柔軟性があります。 

DSC は、特定の特性を持つコンピューター ("ノード") で構成される環境を説明する読みやすいドキュメントである "[構成](https://msdn.microsoft.com/en-us/powershell/dsc/configurations)" の概念を中心として展開します。 これらの特性は、特定の Windows 機能が有効になっていることを確認するなどの単純な場合や、SharePoint の展開のように複雑な場合があります。 

DSC には監視とレポートも組み込まれています。 システムが適合しなくなった場合、DSC は警告を生成し、システムを修正するために機能することができます。 

## Desired State Configuration を使用する利点 ##

構成は、簡単に読み取り、保存、および更新できるように設計されています。 構成では、ターゲット デバイスをあるべき状態にするための手順を記述するのではなく、ターゲット デバイスがあるべき状態を単に宣言します。 これにより、DSC によって構成を学習、導入、実装、および保守するコストが非常に低く抑えられます。 

構成を作成することは、複雑な展開手順が "信頼できる唯一の情報源" として 1 つの場所で取得されることを意味します。 これにより、特定の一連のマシンの展開を繰り返す場合にエラーが発生する可能性が大幅に低下します。 結果として、展開の速度と信頼性が高まります。 これにより、複雑な展開での迅速なターンアラウンドが実現します。

構成は、[PowerShell ギャラリー](https://powershellgallery.com)を介して共有することもできます。 つまり、実行する必要がある作業の一般的なシナリオやベスト プラクティスが既に存在する可能性があります。


## Desired State Configuration と DevOps ##

[DevOps](http://blogs.technet.com/b/ashleymcglone/archive/2015/11/20/devops-for-n00bs-ie-windows-people.aspx) は、迅速な展開と反復を可能にする人、テクノロジ、およびカルチャの組み合わせです。 DSC は、DevOps を意識して設計されました。 単一の構成によって環境を定義することで、開発者は要件を構成にエンコードし、その構成をソース管理にチェックインでき、運用チームではエラーが発生しやすい手動プロセスを使用する必要なく、コードを簡単に展開できるようになります。 

構成は[データ ドリブン](https://msdn.microsoft.com/en-us/powershell/dsc/configdata)でもあるため、開発者が関与することなく、運用チームで簡単に環境を特定および変更することができます。 

## オンプレミスおよびオフプレミスの Desired State Configuration ##

DSC を使用して、オンプレミスとオフプレミスの両方の展開を管理できます。 オンプレミスのソリューションの場合、Desired State Configuration では、[プル サーバー](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver)を使用してマシンの管理を一元化し、それらの状態をレポートできます。 クラウド ソリューションの場合、Desired State Configuration は Windows が使用可能なすべての場所で使用できます。 Azure では、Desired State Configuration のレポート作成を一元化する [Azure Automation](https://azure.microsoft.com/en-us/documentation/services/automation/) など、Desired State Configuration に基づいて構築された特定のサービスも提供しています。 

## DSC と互換性 ##

DSC は Windows Server 2012 R2 で導入されましたが、Windows Management Framework (WMF) パッケージを使用してダウンレベル オペレーティング システムで使用できます。 WMF の詳細については、[PowerShell のホーム ページ](https://msdn.microsoft.com/en-us/powershell/)をご覧ください。 

DSC は、Linux の管理にも使用できます。 詳細については、「[Getting Started with DSC for Linux (Linux 用 DSC の概要)](https://msdn.microsoft.com/en-us/powershell/dsc/lnxgettingstarted)」を参照してください。




<!--HONumber=Oct16_HO1-->


