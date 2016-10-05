# 文字列から構造化オブジェクトを抽出して分析する
これも、ConvertFrom-String コマンドレットに追加機能を導入します。

-   既定では、エクステント テキスト プロパティを削除します。 これを含めるには、-IncludeExtent パラメーターを指定します。

-   MVP やコミュニティのフィードバックから多くの学習アルゴリズムのバグを修正します。

-   新しい -UpdateTemplate パラメーターを使って、学習アルゴリズムの結果をテンプレート ファイル内のコメントに保存します。 これにより、学習プロセス (最も時間のかかる段階) が 1 回限りのコストになります。 エンコードされた学習アルゴリズムを含むテンプレートを使って Convert-String を実行すると、ほぼ瞬時に完了します。


文字列コンテンツから構造化オブジェクトを抽出して分析する
----------------------------------------------------------

Microsoft は、[Microsoft Research](http://research.microsoft.com/) とのコラボレーションにより、新しい **ConvertFrom-String** コマンドレットを追加しました。

このコマンドレットは、基本的な区切り記号による解析と、自動生成したサンプルに基づく解析という 2 つのモードをサポートしています。

区切り記号による解析では、既定で、入力を空白の位置で分割し、生成されるグループにプロパティ名を割り当てます。 区切り記号は、カスタマイズできます。

> 1 \[C:\\temp\] &gt;&gt; "hello World"|ConvertFrom 文字列 |表の書式設定の自動

P1    P2
--    --

このコマンドレットは、[Microsoft Research](http://research.microsoft.com) による [FlashExtract](http://research.microsoft.com/en-us/um/people/sumitg/flashextract.html) の研究成果に基づく、自動生成したサンプルによる解析もサポートしています。

まず手始めに、テキスト ベースのアドレス帳を考えてみましょう。

    Ana Trujillo

    Redmond, WA

    Antonio Moreno

    Renton, WA

    Thomas Hardy

    Seattle, WA

    Christina Berglund

    Redmond, WA

    Hanna Moos

    Puyallup, WA

テンプレートとして使ういくつかの例をファイルにコピーします。

    Ana Trujillo

    Redmond, WA

    Antonio Moreno

    Renton, WA

   

抽出するデータを中かっこで囲み、それに任意の名前を付けます。 **Name** プロパティ (およびそれに関連付けられている他のプロパティ) は複数回出現するので、アスタリスク (\*) を使って、この結果を (1 つのレコードに一連のプロパティを抽出するのではなく) 複数のレコードに抽出することを指定します。

    {Name\*:Ana Trujillo}

    {City:Redmond}, {State:WA}

    {Name\*:Antonio Moreno}

    {City:Renton}, {State:WA}

この一連の例から、**ConvertFrom-String** は、同様の構造を持つ入力ファイルからオブジェクト ベースの出力を自動的に抽出できるようになります。

> 2 \[C:\\temp\]
>
> &gt;&gt; Get-content.\\addresses.output.txt |ConvertFrom 文字列の TemplateFile.\\addresses.template.txt | &gt;&gt;&gt; 表の書式設定の自動
>
> ExtentText                     Name               City     State
> ----------                     ----               ----     -----
> Ana Trujillo...              Ana Trujillo       Redmond  WA Antonio Moreno...            Antonio Moreno     Renton   WA Thomas Hardy...              Thomas Hardy       Seattle  WA Christina Berglund...        Christina Berglund Redmond  WA Hanna Moos...                Hanna Moos         Puyallup WA

抽出されたテキストに追加のデータ操作を行う目的で、**ExtentText** プロパティに、レコードの抽出元から未加工のテキストがキャプチャされます。 この機能にフィードバックを提供するか、例の書き込みに問題があるコンテンツを共有するには、電子メール <psdmfb@microsoft.com>します。



<!--HONumber=Oct16_HO1-->


