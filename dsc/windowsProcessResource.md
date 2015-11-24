#DSC WindowsProcess リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

**WindowsProcess** リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、ターゲット ノード上のプロセスを構成するメカニズムを提供します。

##構文

```
WindowsProcess [string] #ResourceName
{
    Arguments = [string]
    Path = [string]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ DependsOn = [string[]] ]
    [ StandardErrorPath = [string] ]
    [ StandardInputPath = [string] ]
    [ StandardOutputPath = [string] ]
    [ WorkingDirectory = [string] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| 引数| プロセスに渡す引数の文字列を示すは。いくつかの引数を渡す必要がある場合は、この文字列のすべてに配置します。|
| パス| プロセスの実行可能ファイルへのパスを示します。DSC で表示される実行可能ファイルの名前にこのプロパティを設定する場合、 __パス__ 変数です。DSC はチェックされないために、プロセスの存在する必要がありますが設定完全修飾ドメイン名を指定する場合、 __パス__ ここでは変数。|
| Credential| プロセスを起動するための資格情報を示します。|
| 確認します。| プロセスが存在するかどうかを示します。プロセスが存在することを確認するには、"Present"には、このプロパティを設定します。それ以外の場合、「ない」に設定します。既定では"Present です"。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は、' DependsOn ="[リソースの種類] ResourceName"' です。|
| StandardErrorPath| 標準的なエラーを記述するディレクトリ パスを示します。既存のファイルがありますが上書きされます。|
| StandardInputPath| 標準の入力場所を示します。|
| StandardOutputPath| 標準の出力を書き込む場所を示します。既存のファイルがありますが上書きされます。|
| WorkingDirectory| プロセスの現在の作業ディレクトリとして使用される場所を示します。|




