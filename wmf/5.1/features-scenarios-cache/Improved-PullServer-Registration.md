---
title: "プル サーバー登録の機能強化"
author: jaimeo
contributor: Indhu Sivaramakrishnan
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d9f7dea63e6541b673ac6be5ccad59368b301440

---

## プル サーバー登録の機能強化 ##

以前のバージョンの WMF では、ESENT データベースを使用しながら同時に DSC プル サーバーに登録/レポートを要求すると、LCM が登録またはレポートに失敗することがありました。 そのような場合、プル サーバーのイベント ログに "既に使用されているインスタンス名" というエラーが記録されました。
これは、マルチスレッド シナリオで ESENT データベースにアクセスするとき、間違ったパターンが使用されることに起因していました。 WMF 5.1 では、この問題は修正されました。 同時登録または同時レポート (ESENT データベースを含む) が WMF 5.1 では正常に機能します。 この問題は ESENT データベースにのみ関連し、OLEDB データベースには関連しません。 



<!--HONumber=Oct16_HO1-->


