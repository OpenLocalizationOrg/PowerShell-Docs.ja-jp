---
title: "DSC クラス リソースの機能強化"
author: jaimeo
contributor: amitsara
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: b24e70c1e1aaf71487b00fbccaf6edb0f375b888

---

## DSC クラス リソースの機能強化

このリリースでは、WMF 5.0 の次の既知の問題が解消されました。
* クラスベースの DSC リソースの Get() 関数によって複合/ハッシュ テーブル型が返される場合、Get-DscConfiguration は空の値 (null) やエラーを返すことがあります。
* Get-DscConfiguration は、RunAs 資格情報が DSC 構成で使用される場合、エラーを返します。
* コンポジット構成では、クラス ベースのリソースは使用できません。
* Start-DscConfiguration は、クラス ベースのリソースに独自の型のプロパティがあると、応答を停止します。
* クラス ベースのリソースは排他リソースとして使用できません。



<!--HONumber=Oct16_HO1-->


