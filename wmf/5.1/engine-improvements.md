---
title: "WMF 5.1 の PowerShell エンジン機能強化 (プレビュー)"
ms.date: 2016-07-13
keywords: PowerShell, DSC, WMF
description: 
ms.topic: article
author: keithb
manager: dongill
ms.prod: powershell
ms.technology: WMF
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: bb7efc55b1c948c349aa778b700e5cb1277b9762

---

#PowerShell エンジンの機能強化

コア PowerShell エンジンに対する以下の機能強化が WMF 5.1 に実装されました。


## パフォーマンス ##

いくつかの重要な部分でパフォーマンスが向上しました。

- 起動
- ForEach-Object や Where-Object などのコマンドレットに対するパイプライン処理が、約 50% 速くなりました 

機能強化の例をいくつか示します (結果はハードウェアによって異なる場合があります)。 

| シナリオ | 5.0 の時間 (ミリ秒) | 5.1 の時間 (ミリ秒) |
| -------- | :---------------: | :---------------: |
| `powershell -command "echo 1"` | 900 | 250 |
| 最初にこれまで PowerShell 実行します。 `powershell -command "Unknown-Command"` | 30000 | 13000 |
| コマンドの分析のキャッシュが構築: `powershell -command "Unknown-Command"` | 7000 | 520 |
| <code>1..1000000 &#124; % { }</code> | 1400 | 750 |
  
> 起動に関連する 1 つの変更が、一部のサポートされていないシナリオに影響を与える可能性があります。 
> PowerShell は `$pshome\*.ps1xml` ファイルを読み込まなくなりました。これらのファイルは、XML ファイルの処理でファイルと CPU にオーバーヘッドが発生しないよう、C# に変換されています。 
これらのファイルは V2 のサイド バイ サイド インストールをサポートするためにまだ存在するので、ファイルの内容を変更した場合、V5 には影響がなく、影響を受けるのは V2 だけです。 
これらのファイルの内容を変更するシナリオはサポートされていないことに注意してください。

もう 1 つの明らかな変更は、システムにインストールされているモジュールのために PowerShell がエクスポートしたコマンドと他の情報をキャッシュするしくみです。 これまでは、このキャッシュは `$env:LOCALAPPDATA\Microsoft\Windows\PowerShell\CommandAnalysis` ディレクトリに保存されていました。 WMF 5.1 では、キャッシュは `$env:LOCALAPPDATA\Microsoft\Windows\PowerShell\ModuleAnalysisCache` ファイル 1 つだけになっています。
詳細については、「[モジュール分析キャッシュ](scenarios-features.md#module-analysis-cache)」を参照してください。



<!--HONumber=Oct16_HO1-->


