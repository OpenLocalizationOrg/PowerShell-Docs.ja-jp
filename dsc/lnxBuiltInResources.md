---
title: "Linux 用 Desired State Configuration の組み込みリソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 6b001c12885022006003ef3ffe91b7aede07bd17

---

# Linux 用 Desired State Configuration の組み込みリソース

リソースは、PowerShell Desired State Configuration (DSC) スクリプトの作成に使用できる構成要素です。 Linux 用 DSC には、ファイルとフォルダー、パッケージ、環境変数、サービスとプロセスなど、リソースを構成するための一連の組み込み機能が付属します。

## 組み込みリソース 

次の表は、これらのリソースや詳細を説明するトピックへのリンクの一覧を示します。

* [nxArchive リソース](lnxArchiveResource.md) -- 特定のパスでアーカイブ (.tar、.zip) ファイルをアンパックするメカニズムを備えています。
* [nxEnvironment リソース](lnxEnvironmentResource.md) -- ターゲット ノード上の環境変数を管理します。 
* [nxFile リソース](lnxFileResource.md) -- Linux ファイルとディレクトリを管理します。 
* [nxFileLine リソース](lnxFileLineResource.md) -- Linux ファイルの個別の行を管理します。 
* [nxGroup リソース](lnxGroupResource.md) -- ローカル Linux グループを管理します。 
* [nxPackage リソース](lnxPackageResource.md) -- Linux ノード上のパッケージを管理します。
* [nxScript リソース](lnxScriptResource.md) -- ターゲット ノードでスクリプトを実行します。
* [nxService リソース](lnxServiceResource.md) -- Linux サービス (デーモン) を管理します。
* [nxSshAuthorizedKeys リソース](lnxSshAuthorizedKeysResource.md) -- Linux ユーザーの公開 ssh キーを管理します。 
* [nxUser リソース](lnxUserResource.md) -- ローカル Linux ユーザーを管理します。 
  



<!--HONumber=Oct16_HO1-->


