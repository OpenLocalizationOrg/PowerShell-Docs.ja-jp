---
title: "付録 1 - 互換性のあるエイリアス"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 96ad921e-1a57-463e-8e60-424faf8b6ef8
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 61eac49bc710a2435117409b74ba02798cf720da

---

# 付録 1 - 互換性のあるエイリアス
Windows PowerShell には、UNIX と Cmd のユーザーが使い慣れたコマンド名を Windows PowerShell でも使用できるようにするいくつかの移行エイリアスがあります。 最も一般的なエイリアスについて、対応する Windows PowerShell コマンド、および Windows PowerShell の標準エイリアス (存在する場合) と共に以下の表にまとめました。

エイリアスに対応する Windows PowerShell コマンドは、Windows PowerShell から Get-Alias コマンドレットを使用して検索できます。 たとえば、**get-alias cls** と入力します。

```
CommandType     Name                            Definition
-----------     ----                            ----------
Alias           cls                             Clear-Host
```

|CMD コマンド|UNIX コマンド|PS コマンド|PS エイリアス|
|---------------|----------------|--------------|------------|
|**dir**|**%.*ls**|**Get-childitem**|**gci**|
|**cls**|**オフ**|**Clear-Host** (関数)|なし|
|**del、消去、rmdir**|**rm**|**Remove-item**|**参照整合性**|
|**コピー**|**cp**|**コピー項目**|**ci**|
|**移動**|**mv**|**複数の項目に移動します。**|**多重継承**|
|**名前の変更**|**mv**|**項目名の変更**|**rni**|
|**型**|**cat**|**Get-content コマンドレット**|**gc**|
|**cd**|**cd**|**所在地の設定**|**sl**|
|**md**|**mkdir**|**新しい項目**|**ni**|
|なし|**pushd**|**Push-location によって**|なし|
|なし|**popd**|**Pop の場所**|なし|




<!--HONumber=Oct16_HO1-->


