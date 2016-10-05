---
title: "WMF 5.1 (プレビュー) の既知の問題"
ms.date: 2016-07-13
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: krishna
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d4c9e88ddd6cfaec611527d19d00cbd4db9f5d1d

---

#WMF 5.1 (プレビュー) の既知の問題 #

> 注意: この情報は暫定版であり、変更することがあります。

##PowerShell ショートカットを管理者として起動する
WMF がインストールされている場合に、管理者としてショートカットから PowerShell を起動しようとすると、"未定義のエラー" メッセージが表示されることがあります。
管理者以外でショートカットを開きなおすと、その後は管理者でも動作するようになります。

##Pester
このリリースでは、Nano Server で Pester を利用するとき、2 つの問題に注意する必要があります。

* Pester 自体にテストを実行すると、FULL CLR と CORE CLR の違いに起因し、エラーが発生します。 具体的には、Validate メソッドが XmlDocument 型で利用できません。 NUnit 出力ログのスキーマの検証を試行するテストが 6 つありますが、これでエラーが発生することが確認されています。 
* 現在、コード カバレッジの 1 つでエラーが発生します。*WindowsFeature* DSC リソースが Nano Server にないためです。 しかしながら、以上のエラーは一般的に無害であり、無視しても問題ありません。

##操作検証 

* Microsoft.PowerShell.Operation.Validation モジュールの場合、ヘルプ URI が動作しないため、Update-Help は失敗します。



<!--HONumber=Oct16_HO1-->


