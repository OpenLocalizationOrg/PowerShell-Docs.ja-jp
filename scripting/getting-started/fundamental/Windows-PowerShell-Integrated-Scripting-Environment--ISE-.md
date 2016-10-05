---
title: Windows PowerShell Integrated Scripting Environment (ISE)
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: f156b92d-0203-46d2-89c7-b4989d32e3d2
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 061e22c26853664c89adc023d43802628859b9a6

---

# Windows PowerShell Integrated Scripting Environment (ISE)
Windows PowerShell Integrated Scripting Environment (ISE) は、Windows PowerShell エンジンと言語の 2 つのホストのうちの 1 つです。 それを使って、Windows PowerShell コンソールでは使用できない方法でテスト スクリプトを記述し、実行できます。 ISE は、構文の色指定、タブ補完、IntelliSense、視覚的なデバッグ機能、状況依存のヘルプを追加します。

ISE を使用することにより、コンソール ウィンドウでコマンドを実行できるだけでなく、スクリプトのソース コードと ISE に接続できる他のツールを同時に表示するのに使用できるウィンドウもサポートします。 同時に複数のスクリプト ウィンドウを開くこともできます。これは、他のスクリプトまたはモジュールで定義された関数を使用するスクリプトをデバッグする際に特に便利です。

## <a name="BKMK_NEW"></a>新機能
最新のリリースの PowerShell で ISE に追加されたいくつかの機能を次に示します。

### PowerShell 3.0 に追加 (Windows Server 2012、Windows 8)
**IntelliSense** は、文字を入力するときに、一致するコマンドレット、パラメーター、パラメーター値、ファイル、またはフォルダーのメニューを表示することによって、自動的にコマンドを完成させます。

**スニペット**は、記述するスクリプトに簡単に挿入できるコードの短いセクションです。 便利なスニペットのコレクションがあらかじめ用意されており、**New-Snippet** コマンドレットを使ってさらに追加できます。

ISE に機能を追加する**アドオン ツール**は、[Windows PowerShell ISE スクリプト オブジェクト モデル](https://technet.microsoft.com/en-us/library/dd819478.aspx)と対話するコードを記述することによって作成できます。 これらのツールは、タブ形式のウィンドウにコントロールを表示するか、バックグラウンドで非表示で動作します。 **コマンド** アドオンは良い例で、3.0 以降に含まれ、使用可能なコマンドの一覧とそれぞれのヘルプを表示します。

**再起動マネージャーと自動保存**は、クラッシュや予期せぬ再起動による作業の損失を回避するために、2 分ごとにスクリプトを自動的に保存します。

**最近使用した一覧**は、最も頻繁に使用するファイルにより簡単にアクセスできるよう [ファイルを開く] メニューに組み込まれました。

**マージされたコンソール ウィンドウ**。 以前のバージョンの ISE では、コマンド ウィンドウと出力ウィンドウが個別に存在しました。 それらのウィンドウは 1 つのウィンドウに結合され、Windows Powershell コンソールにより近い体裁になっています。

**コマンド ライン スイッチ**。 いくつかの新しいコマンド ライン スイッチにより、ISE の機能の仕方をより詳細に制御できるようになりました。 –NoProfile は、プロファイル スクリプトを実行せずに ISE を起動します。 –Help は、ISE でヘルプ ウィンドウを開きます。 –mta は、“マルチスレッド アパートメント モード” で ISE を起動します。 既定では、シングル スレッドです。

**エディターの新機能**により、コードの作成と読み取りが容易になりました。

-   **XML 構文の色分け**。 ISE エディターは、Windows PowerShell コード構文を色付けするのと同じ方法で XML 構文を色付けします。

-   **かっこの照合**。 ISEWindows PowerShell では、照合するかっこを強調表示して、始めかっこと同じ数の終わりかっこがあることを確認しやすくします。 Ctrl キーを使用して \ [を上にカーソルを開く中かっこに一致する右中かっこを検索します。

-   **アウトライン表示**。 左余白のプラス記号とマイナス記号をクリックして、コードのセクションを折りたたんだり、展開したりできます。 これにより、長いスクリプト内で探しているコードを見つけるのが容易になります。

-   **ドラッグ アンド ドロップによるテキスト編集**。 テキストのブロックを選択し、別の場所にドラッグして移動できます。 Ctrl キーを押しながら選択したテキストをドラッグすると、移動するのではなく、コピーされます。

-   **解析エラーの表示**。 Windows PowerShell は、入力時にスクリプトを調べます。 エラーが検出されると、問題のあるコードの下に赤い波線が表示されます。 示されたエラーをポイントすると、見つかった問題がヒントのテキストに表示されます。

-   **ズーム**。 ISE ウィンドウの右下にあるスライダーを使用することによって、テキストを拡大して読みやすくし、縮小して全体像を見ることができます。

-   **リッチ テキストのコピーと貼り付け**。 ISE からクリップボードにコピーする際、選択したテキストのフォント、サイズ、色の情報が含まれます。

-   **ブロック選択**。 テキストのブロック型の塊を選択するには、Alt キーを押したままマウスでスクリプト ウィンドウ内のテキストを選択するか、または **Alt + Shift + 方向キー**を押します。

### PowerShell 2.0 に追加 (Windows Server 2008 R2、Windows 7)
ISE が PowerShell v2.0 に導入されました。

## Windows PowerShell ISE の実行に関する要件
ISE は、Windows PowerShell v2.0 以降を実行できるすべてのコンピューターで使用できます。 Windows と Windows Server の各バージョンには、Windows PowerShell と ISE のいずれかのバージョンが含まれますが、Windows Management Framework をインストールすることによって、使用可能な最新のバージョンにアップグレードできます。 この検索を実行して使用可能な最新のバージョンを検索します: [ダウンロード](http://www.microsoft.com/en-us/search/DownloadResults.aspx?q=%22windows%20management%20framework%22%20PowerShell&sortby=Relevancy~Descending)。 「プレビュー」というラベルが付いたすべてのエントリはプレリリース コードであり、完全な機能ではないことに注意してください。

> [!NOTE]
> Windows PowerShell ISE にはグラフィカル ユーザー インターフェイスが必要なため、Windows Server の [Server Core] オプションでは実行できません。

## <a name="BKMK_LINKS"></a>関連項目
[Windows PowerShell Integrated Scripting Environment を使用します。](http://technet.microsoft.com/library/cc732148.aspx)




<!--HONumber=Oct16_HO1-->


