---
title: "'using module' の FullyQuilifiedModuleName"
author: jaimeo
contributor: vors
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e09cfe0994ac523fd10658955731a93b6c176c88

---

'using module' の FullyQuilifiedModuleName
=========================

`using module` は PowerShell の他のモジュール関連構造と同様に動作します。

WMF 5.0 の問題
----------

* `using module` でモジュール バージョンを指定する方法がありません。
* システムに複数のバージョンのモジュールが存在する場合、エラーになります。

WMF 5.1
----------

* `ModuleSpecification` [ハッシュ テーブル](https://msdn.microsoft.com/en-us/library/jj136290(v=vs.85).aspx)を使用できます。 このハッシュ テーブルと同じ形式を持つ `Get-Module -FullyQualifiedName`

**例:** `using module @{ModuleName = 'PSReadLine'; RequiredVersion = '1.1'}`

* モジュールに複数のバージョンが存在する場合、PowerShell は `Import-Module` と**同じ解決ロジック**を使用し、エラーを返しません。

* これで `using module` が `Import-Module` と `Import-DscResource` に合わせて調整されます。



<!--HONumber=Oct16_HO1-->


