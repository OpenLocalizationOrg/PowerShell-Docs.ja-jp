---
title: "MSFT_DSCLocalConfigurationManager クラスの TestConfiguration メソッド"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 6ee6189132ff435d3e8a3c93d154bbf301d893c1

---


# MSFT_DSCLocalConfigurationManager クラスの TestConfiguration メソッド

構成ドキュメントを管理ノードに送信し、そのドキュメントに対して現在の構成を検証します。

構文
------

```mof
uint32 TestConfiguration(
  [in]  uint8                          configurationData[],
  [out] boolean                        InDesiredState,
  [out] MSFT_ResourceInDesiredState    ResourcesInDesiredState[],
  [out] MSFT_ResourceNotInDesiredState ResourcesNotInDesiredState[]
);
```

パラメーター
----------

*configurationData* \[in\]  
構成のための環境データ。

*InDesiredState* \[out\]  
制御が戻ったとき、管理ノードが構成ドキュメントで指定された状態であるかどうかを示します。

*ResourcesInDesiredState* \[out\]  
制御が戻ったとき、目的の状態にあるリソースを指定する、**MSFT_ResourceInDesiredState** クラスの埋め込みインスタンスが含まれます。

*ResourcesNotInDesiredState* \[out\]  
制御が戻ったとき、目的の状態ではないリソースを指定する、**MSFT_ResourceNotInDesiredState** クラスの埋め込みインスタンスが含まれます。

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


