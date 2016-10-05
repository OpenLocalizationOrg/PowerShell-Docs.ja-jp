---
title: "MSFT_DSCLocalConfigurationManager クラスの EnableDebugConfiguration メソッド"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 80e29fe72d329d343abb68881d8f1e4a2e7810d1

---


# MSFT_DSCLocalConfigurationManager クラスの EnableDebugConfiguration メソッド

DSC リソースのデバッグを有効にします。

構文
------

```mof
uint32 EnableDebugConfiguration(
  [in] boolean BreakAll
);
```

パラメーター
----------

*BreakAll* \[in\]  
リソース スクリプトのすべての行にブレークポイントを設定します。

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


