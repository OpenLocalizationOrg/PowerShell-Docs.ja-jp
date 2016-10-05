---
title: "MSFT_DSCLocalConfigurationManager クラスの ResourceGet メソッド"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: eb01400b1774ce2ef6e7a3476983404c1329081a

---

# MSFT_DSCLocalConfigurationManager クラスの ResourceGet メソッド

DSC リソースの **Get** メソッドを直接呼び出します。

構文
------

```mof
uint32 ResourceGet(
  [in]  string           ResourceType,
  [in]  string           ModuleName,
  [in]  uint8            resourceProperty[],
  [out] OMI_BaseResource configurations
);
```

パラメーター
----------

*ResourceType* \[in\]  
呼び出すリソースの名前。

*ModuleName* \[in\]  
呼び出すリソースを含むモジュールの名前。

*resourceProperty* \[in\]  
ハッシュ テーブルで、リソースのプロパティ名と値を、それぞれキーと値として指定します。 リソースのプロパティと種類を検出するには、[Get-DscResource](https://technet.microsoft.com/en-us/library/dn521625.aspx) コマンドレットを使用します。

*configurations* \[out\]  
制御が戻ったとき、その構成の埋め込みインスタンスが含まれます。

## 戻り値
------------

成功した場合は 0 を返します。それ以外の場合はエラー コードを返します。

## コメント

これは静的メソッドです。

## 要件
------------
>**MOF:** DscCore.mof

>**名前空間**: Root\Microsoft\Windows\DesiredStateConfiguration


## 関連項目


[**MSFT_DSCLocalConfigurationManager**](msft-dsclocalconfigurationmanager.md)


 

 






<!--HONumber=Oct16_HO1-->


