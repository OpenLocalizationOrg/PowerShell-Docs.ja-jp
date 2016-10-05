---
title: "PowerShell 50 ISE の新機能"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 38648d47-7c27-4b37-a40e-ad29948519c2
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: ababa1b3ce913528a3ac7089d91ef74c5eb27737

---

# Windows PowerShell ISE の新機能
このトピックでは、各バージョンの Windows PowerShell® Integrated Scripting Environment (ISE) に導入された新機能と更新された機能について説明します。

## <a name="overview"></a>機能の説明
Windows PowerShell ISE は、グラフィカルで直感的な環境でスクリプトおよびモジュールを記述、実行、テストすることができるホスト アプリケーションです。 構文の色分け、タブ補完、視覚的なデバッグ機能、Unicode 準拠、状況依存のヘルプなどの主要な機能により、多彩なスクリプトの操作性が提供されます。

Windows PowerShell ISE の概要については、[Windows PowerShell Integrated Scripting Environment の概要](https://technet.microsoft.com/en-us/library/3c1892c2-bf84-4cb6-af26-1f453be9e671)に関するページを参照してください。

## <a name="versions"></a>Windows PowerShell ISE の新機能と変更された機能
次の表に、Windows PowerShell における Windows PowerShell ISE のこのリリースの新機能と変更された機能を示します。

|機能|Windows PowerShell ISE 4.0|Windows PowerShell ISE 3.0|Windows PowerShell ISE 2.0|
|--------------------------|-----------------------------------------------|-----------------------------------------------|-----------------------------------------------|
|**[IntelliSense](#BKMK_Intellisense)**|○|○||
|**[スニペット](#bkmk_snippets)**|○|○||
|**[アドオン ツール](#BKMK_AddOnTools)**|○|○||
|**[再起動マネージャーと自動保存](#BKMK_RestartMgr)**|○|○||
|**[コンソール ウィンドウ](#BKMK_ConsolePane)**|○|○||
|**[最近使用した一覧](#BKMK_MRU)**|○|○||
|**[コマンド ライン スイッチ](#BKMK_CommandLine)**|○|○||
|**[エディターの新機能](#BKMK_NewEditorFeatures)**|○|○||
|**[新しいヘルプ ビューアー] ウィンドウ](#BKMK_NewHelpViewer)**|○|○||
|**[Show コマンドのコマンドレット](#BKMK_ShowCommand)**|○|○||

### <a name="BKMK_Intellisense"></a>IntelliSense
**ISE 3.0 で追加されました。**

IntelliSense は、Windows PowerShell ISE の一部である自動補完アシスタンス機能です。 IntelliSense により、文字を入力していくと、一致する可能性のあるコマンドレット、パラメーター、パラメーター値、ファイル、またはフォルダーのクリック可能なメニューが表示されます。

**この変更はどのような値を追加しますか。**

IntelliSense の追加により、Windows PowerShell ISE を使用してスクリプトを作成する際に、コマンドレットと構文をより簡単に見つけられるようになりました。 また新しいスクリプトの作成中に Windows PowerShell ISE を使用して Windows PowerShell を理解することもできます。

**動作の相違点**

Windows PowerShell ISE 3.0 以降でコマンドレットを入力すると、スクロールとクリックが可能なメニューが表示され、適切なコマンドを参照して選択できます。

### <a name="BKMK_Snippets"></a>スニペット
**ISE 3.0 で追加されました。**

*スニペット* は、Windows PowerShell ISE で作成するスクリプトに挿入できる Windows PowerShell コードの短いセクションです。 Windows PowerShell ISE にはスニペットの既定のセットが付属しています。 Windows PowerShell ISE での作業中に、**New-Snippet** コマンドレットを使用してスニペットを追加できます。

**この変更はどのような値を追加しますか。**

スニペットを使用すると、スクリプトをすばやくアセンブルして作成し、環境を自動化できます。

**動作の相違点**

Windows PowerShell 3.0 以降でスニペットを使用するには、**[編集]** メニューで **[スニペットの開始]** をクリックするか、**[Ctrl-J]** キーを押します。

### <a name="BKMK_AddOnTools"></a>アドオン ツール
**PowerShell 3.0 で追加されました。**

Windows PowerShell ISE では、アドオン ツールをサポートしています。アドオン ツールとは、オブジェクト モデルを使用して追加される Windows Presentation Foundation (WPF) コントロールです。 アドオン ツールは、垂直方向のウィンドウまたは水平方向のウィンドウとしてコンソール内に表示できます。 1 つのウィンドウ内に複数のアドオン ツールがある場合は、タブ形式のコントロールとして表示されます。 また、サード パーティ製のアドオン ツールも追加または削除できます。 アドオン ツールのインポート方法または削除方法の詳細については、「[Windows PowerShell ISE の操作](http://technet.microsoft.com/library/cc732148.aspx)」を参照してください。

**この変更はどのような値を追加しますか。**

アドオンを使用すると、スクリプトの操作性を強化する、または機能を Windows PowerShell ISE に追加できるツールを使用して、Windows PowerShell ISE の拡張とカスタマイズができます。

**動作の相違点**

Windows PowerShell ISE 3.0 以降には、**Commands** アドオンが付属しています。 **Commands** アドオンを使用すると、コマンドレットを参照し、**[スクリプト]** ウィンドウと **[コンソール]** ウィンドウで同時にコマンドレットに関するヘルプにアクセスできます。

追加のアドオンは、**[アドオン]** メニューの **[アドオン ツール Web サイトを開く]** コマンドを使用して検索することができます。

### <a name="BKMK_RestartMgr"></a>再起動マネージャーと自動保存
**PowerShell 3.0 で追加されました。**

Windows PowerShell ISE では、開いているスクリプトを 2 分おきに別の場所に自動的に保存するようになりました。  Windows PowerShell ISE が動作しなくなった場合またはオペレーティング システムが再起動された場合に、Windows PowerShell ISE を再起動すると、最後のセッションで開いていたスクリプトは、保存されていなかった場合でも回復されます。

自動保存の間隔を変更するには、コンソール ウィンドウでコマンド **$psise.Options.AutoSaveMinuteInterval** を実行します。

**この変更はどのような値を追加しますか。**

Windows PowerShell ISE 内での作業で、予期しない再起動が発生した場合、開いているスクリプトが自動的に保存されるようになりました。

**動作の相違点**

Windows PowerShell ISE 2.0 では、再起動が発生した場合、スクリプトは自動的に保存されません。

### <a name="BKMK_MRU"></a>最近使用した一覧
**PowerShell 3.0 で追加されました。**

Windows PowerShell ISE にファイルの最近使用した一覧が用意されるようになりました。 Windows PowerShell ISE でファイルを開くと、ファイルは **[ファイル]** メニューの最近使用した一覧に追加されます。

最近使用した一覧内の既定のファイル数を変更するには、[コンソール] ウィンドウで、**$psise.Options.MruCount** コマンドを実行します。

**この変更はどのような値を追加しますか。**

最近使用した一覧を使用して、頻繁に使用されるファイルに簡単にアクセスできるようになりました。

**動作の相違点**

Windows PowerShell ISE 2.0 には、最近使用した一覧は用意されていません。

### <a name="BKMK_ConsolePane"></a>コンソール ウィンドウ
**PowerShell 3.0 で追加されました。**

Windows PowerShell ISE の最初のリリースで使用できた、独立したコマンド ウィンドウと出力ウィンドウが 1 つのコンソール ウィンドウに統合されました。 コンソール ウィンドウの機能と外観は、標準的な Windows PowerShell コンソールと似ていますが、次のような機能が強化されています (このトピックで、ほとんどの機能を説明します)。

-   XML 構文を含む入力テキストの構文の色分け (出力テキストを除く)

-   IntelliSense

-   かっこの照合

-   エラー表示

-   全角 Unicode のサポート

-   **F1** キーによる状況依存のヘルプ

-   **Ctrl + F1** キーによる状況依存の Show-Command

-   複雑なスクリプトおよび右から左への記述のサポート

-   フォントのサポート

-   ズーム

-   行選択モードとブロック選択モード

-   **上**方向キーを押して履歴をコンソールに表示したときにコマンド ラインに入力されたコンテンツを保存する機能

**この変更はどのような値を追加しますか。**

これらのコンソール ウィンドウの変更を追加したことにより、コンソール インターフェイスとの一貫性がより向上したスクリプトの操作性が提供されます。

**動作の相違点**

Windows PowerShell ISE 2.0 では、コマンド ウィンドウと出力ウィンドウは個別に表示されます。

### <a name="BKMK_CommandLine"></a>コマンド ライン スイッチ
**PowerShell 3.0 で追加されました。**

コマンド ライン (「**Powershell_ise.exe**」を入力) から Windows PowerShell ISE を起動する場合、次の新しいコマンド ライン スイッチを追加できます。

-   *-NoProfile*: **$profile** を実行せずに Windows PowerShell ISE を起動する

-   *-Help*: ヘルプ ウィンドウを表示する

-   *-mta*: マルチスレッド アパートメント モードで Windows PowerShell ISE を起動する。 Windows PowerShell ISE の既定の操作モードは、1 つのシングル スレッド アパートメント モード、つまり *-sta* です。

**この変更はどのような値を追加しますか。**

これらのコマンド ライン スイッチを追加すると、Windows PowerShell ISE が動作する環境を制御することができます。

**動作の相違点**

Windows PowerShell ISE 2.0 では、これらのコマンド ライン スイッチは認識されません。

### <a name="BKMK_NewEditorFeatures"></a>エディターの新機能
**PowerShell 3.0 で追加されました。**

その他の Windows PowerShell ISE 編集機能として、次のものがあります。

-   **XML 構文の色分け**。Windows PowerShell ISE では、Windows PowerShell 構文を色分けする方法と同じ方法で XML 構文を色分けするようになりました。

-   **かっこの照合**。Windows PowerShell ISE は、かっこの照合と強調表示の機能を搭載し、次のように使用できます。たとえば、始めかっこを選択している場合、**[一致する項目に移動]** コマンドまたは **Ctrl + ]** を使用すると、終わりかっこが見つかります。

-   **アウトライン表示**。スクリプト ウィンドウでは、アウトラインがサポートされます。これにより、左余白のプラス記号またはマイナス記号をクリックすると、コードのセクションを折りたたんだり展開したりできます。 かっこ、または **#region** タグと **#endregion** タグを使用して、折りたたみ可能なセクションの先頭または末尾をマークすることができます。 すべての領域を展開する、または折りたたむには、**Ctrl + M** キーを押します。

-   **ドラッグ アンド ドロップ テキスト編集**。Windows PowerShell ISE で、ドラッグ アンド ドロップによるテキスト編集がサポートされるようになりました。 任意のテキストのブロックを選択し、そのテキストをドラッグしてエディターまたはコンソール内の別の場所に移動できます。 Ctrl キーを押したまま選択したテキストをドラッグし、マウス ボタンを放すと、テキストが新しい場所にコピーされます。 以前のバージョンの Windows PowerShell ISE と同様に、このバージョンの Windows PowerShell ISE では、ファイルを Windows PowerShell ISE 上にドラッグ アンド ドロップすると、Windows PowerShell ISE でファイルが開かれます。

-   **解析エラーの表示**。解析エラーは赤い下線で示されます。 示されたエラーをポイントすると、コード内で見つかった問題がヒントのテキストに表示されます。

-   **ズーム**。コンソールのコンテンツのズーム倍率を設定するには、ズーム スライダー (Windows PowerShell ISE ウィンドウの右下隅にあります) を使用するか、またはコンソール ウィンドウでコマンド「**$psise.options.Zoom**」を入力します。

-   **リッチ テキストのコピーと貼り付け**。Windows PowerShell ISE でクリップボードにコピーすると、元の選択範囲のフォント、サイズ、および色の情報が保存されます。

-   **ブロック選択**。テキストのブロックを選択するには、Alt キーを押したままマウスでスクリプト ウィンドウ内のテキストを選択するか、または **Alt + Shift + 方向キー**を押します。

**この変更はどのような値を追加しますか。**

追加の編集機能によって、より一貫性のある強力な編集環境が提供されます。

**動作の相違点**

これらの編集の拡張機能は Windows PowerShell ISE 2.0 にはなかった機能です。

### <a name="BKMK_NewHelpViewer"></a>新しいヘルプ ビューアー ウィンドウ
**PowerShell 3.0 で追加されました。**

カーソルがコマンドレット内にあるかコマンドレットの一部を強調表示しているときに **F1** キーを押すと、強調表示されたコマンドレットに関する状況依存のヘルプが新しいヘルプ ビューアーで開かれます。 Windows PowerShell の About ヘルプを表示するには、コンソール ウィンドウで「**operators**」と入力し、**F1** キーを押します。

この機能を使用する前に、Microsoft Web サイトから Windows PowerShell のヘルプ トピックの最新バージョンをダウンロードしてください。 ヘルプ トピックをダウンロードする最も簡単な方法は、管理者として Windows PowerShell を実行中に、コンソール ウィンドウで **Update-Help** コマンドレットを実行することです。

**F1** キーを押したときにヘルプを検索する場所を変更することができます。 **[ツール]**/**[オプション]** メニューで、**[全般設定]** タブの **[その他の設定]** の下で、**[オンライン コンテンツの代わりにローカル ヘルプ コンテンツを使用する]** ボックスをオンまたはオフにすることができます。 オンにした場合、クライアントはモジュール フォルダーで検出されたダウンロード済みのヘルプ内でコマンドレット Help を検索します。  チェック ボックスをオフにした場合は、クライアントは、コマンドレット Help を TechNet ライブラリで検索します。

**この変更はどのような値を追加しますか。**

現在のコマンドレットまたはスクリプトを離れることなく状況依存のヘルプを使用すると、シームレスに学習を行うことができます。

**動作の相違点**

以前のバージョンの Windows PowerShell ISE で F1 キーを押すと、ローカル コンピューター上のヘルプ ファイルが開きました。 Windows PowerShell ISE 3.0 以降では、検索と構成が可能なコマンドレットのヘルプを含むウィンドウが開きます。 このヘルプ エクスペリエンスは Windows PowerShell ISE 3.0 の新機能であり、更新可能なヘルプは Windows PowerShell 3.0 の新機能です。

### <a name="BKMK_ShowCommand"></a>Show コマンドのコマンドレット
**PowerShell 3.0 で追加されました。**

**Show-Command** コマンドレットを使用すると、グラフィカルなフォームに情報を入力してコマンドレットや関数を構成または実行できます。 フォームを使用すると、グラフィカルな環境でユーザーが Windows PowerShell で作業できます。 また、上級のスクリプト作成者は **Show-Command** を使用して、簡単な Windows PowerShell ベースの GUI を作成できます。

**この変更はどのような値を追加しますか。**

Windows PowerShell スクリプトで **Show-Command** を使用すると、ユーザーが使い慣れたグラフィカルな環境を提供できます。 また、**Show-Command** は入門ユーザーが Windows PowerShell を学習するのにも役立ちます。

**動作の相違点**

Show-Command は Windows PowerShell ISE 3.0 の新機能です。

## <a name="BKMK_LINKS"></a>関連項目
Windows PowerShell での Windows PowerShell ISE の使用に関する詳細については、次のリンクを参照してください。

-   [Windows PowerShell Integrated Scripting Environment を使用します。](../core-powershell/ise/Using-the-Windows-PowerShell-ISE.md)

-   [TechNet Wiki の ISE](http://social.technet.microsoft.com/wiki/search/searchresults.aspx?q=ISE)

-   [スクリプト センター](http://technet.microsoft.com/scriptcenter/default)




<!--HONumber=Oct16_HO1-->


