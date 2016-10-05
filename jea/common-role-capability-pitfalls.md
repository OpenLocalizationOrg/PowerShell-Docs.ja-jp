---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "ロール機能のよくある落とし穴"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 2b2dfd9c39fe5d7bf4a52032653108729715e6bf

---

### ロール機能のよくある落とし穴
本手順の実行時に陥りやすいいくつかの落とし穴について説明します。
エンドポイントの変更時、また新規作成時にこれらの問題を特定し、対処する方法の概要を以下に紹介します。

#### 関数とコマンドレット
PowerShell で記述された PowerShell コマンドを PowerShell 関数と呼びます。
特定の .NET クラスとして記述された PowerShell コマンドは、PowerShell コマンドレットです。
コマンドの種類を確認するには、`Get-Command` を実行します。

#### VisibleProviders
コマンドに必要なすべてのプロバイダーを公開する必要があります。
最も一般的なプロバイダーとして FileSystem プロバイダーがありますが、他に Registry プロバイダーなどの公開が必要な場合もあります。
プロバイダーの概要については、[Hey, Scripting Guy のブログ投稿](http://blogs.technet.com/b/heyscriptingguy/archive/2015/04/20/find-and-use-windows-powershell-providers.aspx)をご覧ください。
プロバイダーの公開時の注意事項: JEA セッションで直接プロバイダーを公開するより、基盤となるプロバイダーを操作する独自の関数を定義するほうが適している場合もあります。
この方法でも、ファイル、レジストリ キーなどを操作する権限や、カスタムの検証ロジックを使用して**どの**ファイルやレジストリ キーを管理するか、それらに対するコントロールをユーザーに付与できます。




<!--HONumber=Oct16_HO1-->


