---
title: "Windows PowerShell Desired State Configuration の概要"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 1a796658eb30bdf5c37ea3677f94767260a34b45

---

# Windows PowerShell Desired State Configuration の概要 

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

このトピックでは、Windows PowerShell における Windows PowerShell Desired State Configuration (DSC) 機能について説明します。 このトピックでは、DSC の概要を把握し、DSC を理解して使用するために必要なドキュメント リソースを見つけることができます。

## 機能の説明
DSC は、ソフトウェア サービスの構成データを展開および管理し、これらのサービスの実行環境を管理できる、Windows PowerShell の新しい管理プラットフォームです。

DSC は、ソフトウェア環境をどのように構成するかを宣言によって指定するために使用する、Windows PowerShell の言語拡張、新しい Windows PowerShell コマンドレット、およびリソースのセットを提供します。 また、既存の構成を保守し、管理するための手段も提供します。

## 実際の適用例
一連のコンピューター (ターゲット ノードとも呼ばれます) を自動化された方法で構成および管理するために、組み込みの DSC リソースを使用できるシナリオ例を次に示します。

* サーバーの役割と機能の有効化または無効化
* レジストリ設定の管理
* ファイルとディレクトリの管理
* プロセスとサービスの開始、停止、および管理
* グループおよびユーザー アカウントの管理
* 新しいソフトウェアの展開
* 環境変数の管理
* Windows PowerShell スクリプトの実行
* 望ましい状態からずれた構成の修正
* 特定のノードでの実際の構成状態の検出

## 主要概念
DSC は、システムの構成、展開、および管理に使用する宣言型プラットフォームです。 次の 3 つの主要なコンポーネントで構成されます。

* [構成](configurations.md)は、リソースのインスタンスを定義および構成する宣言型の PowerShell スクリプトです。 構成を実行すると、DSC (および構成によって呼び出されるリソース) は、構成によってレイアウトされた状態でシステムが存在するように、単に、指定されたことを実現します。 DSC 構成は、べき等でもあります。ローカル構成マネージャー (LCM) によって、構成によって宣言された状態でマシンが構成されることが、継続的に保証されます。
* リソースは、サブシステムのさまざまなコンポーネントをモデル化し、その状態の変更の制御フローを実装するために作成される DSC の不可欠な構成要素です。 PowerShell モジュール内に存在し、ファイルまたは Windows プロセスのように汎用的なものをモデル化したり、IIS サーバーまたは Azure で実行されている VM のように具体的なものをモデル化したりするように作成できます。
* ローカル構成マネージャー (LCM) は、DSC でのリソースと構成の間の対話を容易にするエンジンです。 LCM は、リソースによって実装される制御フローを使用してシステムを定期的にポーリングし、構成によって配置された状態が維持されるようにします。 システムが状態外の場合、LCM はリソース内のより多くのロジックを使用して、構成の宣言に従って、指定されたことを実現します。 

DSC には、構成の作成を許可し、DSC リソースのビルドに役立ち、構成を呼び出して、LCM を管理する、さまざまな新しい言語キーワード、コマンドレット、およびツールも含まれています。 これらのコマンドレットの多くは、Windows 8.1 の PSDesiredStateConfiguration モジュールの一部として含まれています (`Start-DscConfiguration`、`Set-DscLocalConfigurationManager`、および `Get-DscResource` を含む)。 xDscResourceDesigner ([PowerShell ギャラリー](https://www.powershellgallery.com/packages/xDSCResourceDesigner/)にあります) は、DSC リソースの開発を簡略化するためのコマンドレットのコレクションです。

## 参照
* [DSC 構成](configurations.md)
* [DSC リソース](resources.md)
* [ローカル構成マネージャーの構成](metaConfig.md)




<!--HONumber=Oct16_HO1-->


