# 発行操作中にモジュールの依存関係を準備するためのロジック
1.  RequiredModules の一部としてリストされているモジュールは、依存関係としてと見なされます。
2.  モジュールのベースが指定されたモジュール ベース] の下ではありません、NestedModules の一部としてリストされているモジュールは、依存関係としてと見なされます。

3.  依存関係上必要があります、同じターゲット リポジトリで提供される、それ以外の場合発行操作を実行すると、エラーが発生します。
    例: 'SnippetPx' がリポジトリで使用できない場合は、以下のエラーがスローされます。
    ```powershell
    Publish-PSArtifactUtility : PowerShellGet cannot resolve the module dependency 'SnippetPx' of the module 'TypePx' on the repository 'LocalRepo'. Verify that the dependent module 'SnippetPx' is available in the repository 'LocalRepo'. If this dependent
    'SnippetPx' is managed externally, add it to the ExternalModuleDependencies entry in the PSData section of the module manifest.
    ```
4.  一部のモジュールの依存関係を外部で管理できる、その場合は、モジュール マニフェストの PSData セクションに ExternalModuleDependencies エントリに追加する必要があります。
    上記のエラー メッセージ内の部分の下
    ```powershell
    If this dependent 'SnippetPx' is managed externally, add it to the ExternalModuleDependencies entry in the PSData section of the module manifest.
    ```

*モジュールのインストール時に準備された依存関係上一覧を使用、依存関係をインストールするため。*

*モジュールの依存関係が $env:path で使用可能なことを確認してください: 公開操作の中にシステムでは、psmodulepath 環境変数です。*


<!--HONumber=Oct16_HO1-->


