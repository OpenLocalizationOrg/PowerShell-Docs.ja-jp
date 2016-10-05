---
title: "MSFT_DSCLocalConfigurationManager クラスの ApplyConfiguration メソッド"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 9fad1b321c56d57e2671636511a15b7ccc640f9c

---

# MSFT_DSCLocalConfigurationManager クラスの ApplyConfiguration メソッド

構成エージェントを使用して、保留中の構成を適用します。 

保留中の構成がない場合は、現在の構成を再適用します。


## 構文
------

```mof
uint32 ApplyConfiguration(
  [in] boolean force
);
```

## パラメーター
----------

*force* \[in\]  
**true** の場合、保留中の構成があっても、現在の構成が再適用されます。

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


