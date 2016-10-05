# 互換性のある PowerShell のエディションを持つ項目
バージョン 5.1 から、PowerShell はさまざまな機能セットとプラットフォーム互換性を備える別のエディションで使用できます。

- **デスクトップ エディション:** .NET Framework 上に構築されており、Server Core や Windows Desktop などの Windows の完全エディションで実行する PowerShell のバージョンを対象とするスクリプトおよびモジュールとの互換性を提供します。
- **コア エディション:** .NET Core 上に構築されており、Nano Server や Windows IoT などの Windows の縮小エディションで実行する PowerShell のバージョンを対象とするスクリプトおよびモジュールとの互換性を提供します。

## PowerShell Gallery がサポートされている PSEditions メタデータが抽出されするため、項目のフィルターに互換性のある PowerShell の特定のエディション

項目が、互換性のある PSEditions 指定場合 'PowerShell エディション' の一部として表示されます表示] タブの項目と項目の結果。
![PSEditions と項目の表示] ページ](Images/ItemDisplayPageWithPSEditions.PNG)

## ギャラリー PowerShellCore で動く UI で項目を検索します。
タグを使用します。"PSEdition_Desktop"とタグ:"PSEdition_Core"PowerShell ギャラリー項目のフィルターにします。

### タグを使用します。 PowerShell Core エディションと互換性のある項目を検索するには、"PSEdition_Core"です。
![コア PSEdition と互換性のあるアイテムの検索結果](Images/SearchResultsWithPSEditions.PNG)

### タグを使用します。 PowerShell デスクトップのエディションと互換性のある項目を検索するには、"PSEdition_Desktop"です。
![デスクトップ PSEdition と互換性のあるアイテムの検索結果](Images/SearchResultsWithPSEdition_Desktop.PNG)

## 詳細については、オーサリングと互換性のある PowerShell のエディションに項目を検索します。
### [PSEditions でモジュール](../psget/module/modulewithpseditionsupport.md)
### [PSEditions を使用してスクリプト](../psget/script/scriptwithpseditionsupport.md)

<!--HONumber=Oct16_HO1-->


