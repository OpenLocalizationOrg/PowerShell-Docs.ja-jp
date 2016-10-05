---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "JEA のレポート"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d867a6462e9fa8b6e16c8c2103899c72b380116c

---

# JEA のレポート
JEA では権限のないユーザーでも特権コンテキストを実行できるため、ログ記録と監査が極めて重要です。
このセクションでは、ログ記録とレポートに役立つツールを実行します。

## JEA 操作のレポート
### Over-the-Shoulder トランスクリプト
PowerShell セッションで何が行われているかその概要を取得する最も簡単な方法の 1 つとして、入力している人を覗き見ることです。
使っているコマンド、そのコマンドによる出力がわかります。何も問題はありません。
または、あったとしても、少なくとも情報を得ることができます。
PowerShell トランスクリプションは、事後的に同類のビューを提供するように設計されています。

セッション構成で TranscriptDirectory フィールドを使用した場合、PowerShell は特定のセッションで実行されたすべてのアクションのトランスクリプトを自動的に記録します。
セッションのトランスクリプトは、次のドキュメントにあります: $env:ProgramData\JEAConfiguration\Transcripts。

おわかりのとおり、トランスクリプトは "接続されているユーザー"、RunAs ユーザー、セッションで実行されたコマンドなどを記録します。
PowerShell トランスクリプションについて詳しくは、[こちらのブログ投稿](http://blogs.msdn.com/b/powershell/archive/2015/06/09/powershell-the-blue-team.aspx)をご覧ください。

### PowerShell イベント ログ
モジュールのログ記録を有効にした場合、すべての PowerShell アクションも正規の Windows イベント ログに記録されます。
これはトランスクリプトに比べて多少扱いにくいものの、詳細な情報が提供されるため便利です。

PowerShell の操作ログでは、モジュールのログ記録を有効にしている場合、呼び出された各コマンドがイベント ID 4104 によって記録されます。

### その他のイベント ログ
PowerShell ログおよびトランスクリプトと異なり、他のログ記録メカニズムでは "接続されているユーザー" は取得されません。
他のログと PowerShell ログの整合性を図るには、これらを相互に関連付ける必要があります。

"Windows リモート管理" 操作ログでは、イベント ID 193 により、接続しているユーザーの SID と名前に加え、RunAs 仮想アカウントの SID が記録されこの関連付けがサポートされます。
RunAs 仮想アカウントの名前の末尾に、接続しているユーザーのドメインとユーザー名が含まれていることにお気付きかもしれません。

## JEA 構成のレポート
### Get-PSSessionConfiguration
使用している環境の状態の正確なレポートを取得するために、コンピューターにセットアップした JEA エンドポイントの数を把握しておくことが重要です。
`Get-PSSessionConfiguration` のみを実行します。

### Get-PSSessionCapability
JEA エンドポイントを介して特定のユーザーの機能を手動でレポートすることは、非常に複雑になる場合があります。
いくつかのロール機能の調査が必要になる場合があります。
幸い、ここでは Get-PSSessionCapability コマンドレットを使用できます。

これをテストするには、管理者の PowerShell プロンプトから次のコマンドを実行します。
```PowerShell
Get-PSSessionCapability -Username 'CONTOSO\OperatorUser' -ConfigurationName JEADemo
```




<!--HONumber=Oct16_HO1-->


