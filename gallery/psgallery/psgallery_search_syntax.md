# ギャラリーの検索構文

PowerShell Gallery には、検索結果を絞り込む単語、語句、およびキーワード式を使用できるテキストの検索ボックスが用意されています。

## キーワードで検索

    dsc azure sql

検索は 3 つのすべてのキーワードを含む関連するドキュメントを検索する最善の努力を行うし、一致するドキュメントを返します。

## フレーズとキーワードを使用して検索します。

    "azure sql" deployment

引用符の間での語句を入力する ("") を個別のキーワードではなく特定のフレーズの検索対象を変更します。
一致するドキュメントでは、フレーズ"azure sql"、大文字と小文字のバリエーションを含むなど通常含める必要があります。"Azure SQL"も通常という単語を含む '配置' です。

## フィールドでフィルター処理

検索するフィールド名を持つ単語を付けることによって他の特定のフィールドまたは特定の項目の ID (または 'Id' または 'id')、検索できます。

現在検索可能フィールドは、'Id'、'Version'、'タグ'、'Author'、'Owner'、'関数'、'コマンドレット'、'DscResources' および 'PowerShellVersion' です。

[ID とタイトルの間の違いは何ですか。 ID は、コンソールで使用する名前です。 タイトルは検索結果の項目] ページの上部に表示される内容です。]

## 例

    ID:"PSReadline"
    id:"AzureRM.Profile"

それぞれの ID フィールドで"PSReadline"または"AzureRM.Profile"を持つ項目を検索します。

    Id:"AzureRM.Profile"

ID フィールドで"AzureRM.Profile"を持つ項目を検索する別の方法です。

'Id' フィルターは、部分文字列の一致、そのため、次を検索する場合を示します。

    Id:"azure"
    
'AzureRM.Profile' と 'Azure.Storage' のような結果が得られます。

単一のフィールドに、複数のキーワードを検索することもできます。 またはを混在させるし、フィールドに一致します。

    id:azure tags:intellisense
    id:azure id:storage

句の検索を行うことができます。

    id:"azure.storage"


DSC のタグを持つすべての項目を検索します。

    Tags:"DSC"

指定の関数ですべての項目を検索します。

    Functions:"Update-AzureRM"

指定したコマンドレットを使用したすべての項目を検索します。
    
    Cmdlets:"Get-AzureRmEnvironment"

指定の DSC リソースの名前を持つすべての項目を検索します。

    DscResources:"xArchive"

指定した PowerShellVersion ですべての項目を検索するには

    PowerShellVersion:"5.0"
    PowerShellVersion:"3.0"
    PowerShellVersion:"2.0"


最後に、'コマンド' など、サポートされていません] フィールドを使用する場合だけそれを無視し、すべてのフィールドを検索します。 したがって、次のクエリ

    commands:blobs storage
    
次のクエリとまったく同じ解釈されます。

    blobs storage



<!--HONumber=Oct16_HO1-->


