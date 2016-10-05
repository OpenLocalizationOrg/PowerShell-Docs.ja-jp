---
title: "既知の問題または制限の書き込みのテンプレート例"
contributor: 
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: cfe0a6562743f1df81acb81e33c120cb67f9042c

---

>注: わかりやすいタイトルと簡単な説明を提供します

## 例: Erroneous ExecutionPolicy エラー ##
Windows 7 で PowerShell モジュールと DSC リソースを使用すると、ExecutionPolicy についてレポートされるエラーとなる場合があります。

### 解決方法

解決するには、管理者特権の PowerShell セッション (管理者として実行) で、次のコマンドを実行して **ExecutionPolicy** を **RemoteSigned** に設定します。

```powershell
Set-ExecutionPolicy RemoteSigned
```



<!--HONumber=Oct16_HO1-->


