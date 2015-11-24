#DSC ファイル リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

ファイルのリソースで Windows PowerShell 必要な状態 Configuration (DSC) では、ターゲット ノードのファイルとフォルダーを管理するメカニズムを提供します。

##構文

```
File [string] #ResourceName
{
    DestinationPath = [string]
    [ Attributes = [string[]] { Archive | Hidden | ReadOnly | System }]
    [ Checksum = [string] { CreatedDate | ModifiedDate | SHA-1 | SHA-256 | SHA-512 } ]
    [ Contents = [string] ]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present } ] 
    [ Force = [bool] ]
    [ Recurse = [bool] ]
    [ DependsOn = [string[]] ]
    [ SourcePath = [string] ]
    [ Type = [string] { Directory | File } ] 
    [ MatchSource = [bool] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| DestinationPath| ファイルまたはディレクトリの状態を保証する場所を示します。|
| 属性| 対象となるファイルまたはディレクトリの属性の目的の状態を指定します。|
| チェックサム| 2 つのファイルが同じであるかどうかを決定するときに使用するチェックサムの種類を示します。場合 __チェックサム__ が指定されていない、比較のため、ファイルまたはディレクトリ名のみを使用します。有効な値が含まれます。 sha-1、sha-256、SHA 512、createdDate、modifiedDate します。|
| 内容| 特定の文字列など、ファイルの内容を指定します。|
| Credential| このようなアクセスが必要な場合は、ソース ファイルなど、リソースにアクセスするには、必要な資格情報を示します。|
| 確認します。| ファイルまたはディレクトリが存在するかどうかを示します。「すべて」をファイルまたはディレクトリが存在しないことを確認するには、このプロパティを設定します。ファイルまたはディレクトリが存在することを確認するには、"Present"に設定します。既定では"Present です"。|
| Force| 特定のファイル操作 (ファイルを上書きするが空でないディレクトリを削除するなど) すると、エラーが発生します。Force プロパティを使用すると、このようなエラーがよりも優先します。既定値は __$false__です。|
| 再帰的に検索します。| サブディレクトリが含まれるかどうかを示します。このプロパティを設定 __$true__ にサブディレクトリを含めるように指定します。既定値は __$false__です。**注**。 このプロパティは、ディレクトリに、Type プロパティが設定されている場合にのみ有効です。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|
| SourcePath| ファイルまたはフォルダーのリソースのコピー元のパスを示します。|
| 種類| 構成されているリソースが、ディレクトリまたはファイルのかどうかを示します。リソースがディレクトリであることを示すには、「ディレクトリ」には、このプロパティを設定します。リソースが、ファイルであることを示すには、"File"に設定します。既定値は、「ファイル」です。|
| MatchSource| 場合の既定値に設定 __$false__, 、ソース (たとえば、ファイル A、B、および C) 上のファイルに追加されます、変換先、構成が適用される初めてします。ソースには、新しいファイル (D) を追加する場合に追加されません先の構成は後で再適用している場合でもです。値が場合 __$true__, 、(この例ではファイル D) などのソースで検出された、その後、新しいファイルが、構成が適用されるたびに、変換先に追加します。|

##例

次の例は、ファイル リソースを使用して、ディレクトリのパスがあることを確認する方法を示しています。 `C:\Users\Public\Documents\DSCDemo\DemoSource` で、ソース コンピューター ("プル"サーバーなど) が存在 (およびすべてのサブディレクトリ) のターゲット ノード上。 また、confirmatory メッセージを完了すると、ログに書き込み、ログ記録操作の前に、ファイルの確認操作を実行することを確認するステートメントが含まれています。

```powershell
Configuration FileResourceDemo
{
    Node "localhost"
    {
        File DirectoryCopy
        {
            Ensure = "Present"  # You can also set Ensure to "Absent"
            Type = "Directory" # Default is "File".
            Recurse = $true # Ensure presence of subdirectories, too
            SourcePath = "C:\Users\Public\Documents\DSCDemo\DemoSource"
            DestinationPath = "C:\Users\Public\Documents\DSCDemo\DemoDestination"    
        }

        Log AfterDirectoryCopy
        {
            # The message below gets written to the Microsoft-Windows-Desired State Configuration/Analytic log
            Message = "Finished running the file resource with ID DirectoryCopy"
            DependsOn = "[File]DirectoryCopy" # This means run "DirectoryCopy" first.
        }
    }
}
```




