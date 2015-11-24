#DSC Linux nxFile リソース用

**NxFile** リソース PowerShell 必要な状態 Configuration (DSC) ではファイルおよび Linux のノード上のディレクトリを管理するメカニズムを提供します。

##構文

```
nxFile <string> #ResourceName
{
    DestinationPath = <string>
    [ SourcePath = <string> ]
    [ Ensure = <string> { Absent | Present }  ]
    [ Type = <string> { directory | file | link } ]
    [ Contents = <string> ]
    [ Checksum = <string> { ctime | mtime | md5 }  ]
    [ Recurse = <bool> ]
    [ Force = <bool> ]
    [ Links = <string> { follow | manage } ]
    [ Group = <string> ]
    [ Mode = <string> ]
    [ Owner = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| DestinationPath| ファイルまたはディレクトリの状態を保証する場所を指定します。|
| SourcePath| ファイルまたはフォルダーのリソースのコピー元のパスを指定します。このパスがローカル パスでは、あります。 または、 `http、https、ftp` URL。リモート `http、https、ftp` Url でのみはされたときにサポートの値、 **型** プロパティは、ファイルです。|
| 確認します。| ファイルが存在するかどうかを確認するかどうかを判断します。ファイルが存在することを確認するには、"Present"には、このプロパティを設定します。ファイルが存在しないことを確認する「ない」に設定します。既定値は、"Present"です。|
| 種類| 構成されているリソースは、ディレクトリまたはファイルかどうかを指定します。リソースがディレクトリであることを示すには、「ディレクトリ」には、このプロパティを設定します。リソースが、ファイルであることを示すには、[ファイル] に設定します。既定値は、[ファイル]|
| 内容| 特定の文字列など、ファイルの内容を指定します。|
| チェックサム| 2 つのファイルが同じであるかどうかを決定するときに使用する型を定義します。場合 **チェックサム** が指定されていない、比較のため、ファイルまたはディレクトリ名のみを使用します。値は、:"mtime"、"ctime"または"md5"です。|
| 再帰的に検索します。| サブディレクトリが含まれるかどうかを示します。このプロパティを設定 **$true** にサブディレクトリを含めるように指定します。既定値は **$false**です。**注:** ときにこのプロパティは有効なのみ、 **型** をディレクトリにプロパティを設定します。|
| Force| 特定のファイル操作 (ファイルを上書きするが空でないディレクトリを削除するなど) すると、エラーが発生します。使用して、 **Force** プロパティには、このようなエラーがよりも優先されます。既定値は **$false**です。|
| リンク| シンボリック リンクの目的の動作を指定します。このプロパティは、シンボリック リンクをたどるし、(たとえば、リンクのターゲットで機能するには、「フォロー」に設定リンクの代わりに、ファイルをコピー) します。(たとえば、リンクで機能するには、「管理」するには、このプロパティを設定します。リンク自体をコピー) します。シンボリック リンクを無視するには、「無視」するには、このプロパティを設定します。|
| グループ| 名前、 **グループ** をファイルまたはディレクトリを所有します。|
| モード| 8 進数、シンボルの表記法では、リソースの必要なアクセス許可を指定します。(たとえば、777 または rwxrwxrwx など)。シンボルの表記を使用している場合は、ディレクトリまたはファイルを示す最初の文字を提供してください。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##追加情報

Linux、Windows、テキスト ファイルでさまざまな改行文字を使用して、既定では、および Linux コンピューターでいくつかのファイルを構成するときに予期しない結果が生じる __nxFile__です。 予期しない改行文字によって引き起こされる問題を回避しながら、Linux のファイルのコンテンツを管理する複数の方法はあります。

手順 1: (http、https、または ftp) のリモート ソースからファイルをコピーします。 目的の内容を Linux 上のファイルを作成し、ノードを構成するアクセスできる web または ftp サーバーでステージングします。 定義する、 __SourcePath__ 内のプロパティ、 __nxFile__ ファイルへの web または ftp の URL を使用してリソースです。

```
Import-DSCResource -Module nx
Node $Node
{
nxFile resolvConf
{
    SourcePath = "http://10.185.85.11/conf/resolv.conf"
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"

}

}
```


手順 2: は、PowerShell スクリプトのファイルの内容を読み取る [Get-content](https://technet.microsoft.com/en-us/library/hh849787.aspx) 設定した後、 __$OFS__ Linux の改行文字を使用するプロパティです。


```
Import-DSCResource -Module nx
Node $Node
{
$OFS = "`n"
$Contents = Get-Content C:\temp\resolv.conf

nxFile resolvConf
{
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"
    Contents = "$Contents"
}

}
```


手順 3: では、PowerShell 関数を使用して、Windows の改行を Linux 改行文字に置き換えます。

```
Function LinuxString($inputStr){
    $outputStr = $inputStr.Replace("`r`n","`n")
    $ouputStr += "`n"
    Return $outputStr
}

Import-DSCResource -Module nx
Node $Node
{

$Contents = @'
search contoso.com
domain contoso.com
nameserver 10.185.85.11
'@

$Contents = LinuxString $Contents

nxFile resolvConf
{
    DestinationPath = "/etc/resolv.conf"
    Mode = "644"        
    Type = "file"
    Contents = $Contents

}
}
```

##例

次の例により、ディレクトリ `/選択/mydir` が存在する場合、指定した内容を持つファイルがこのディレクトリが存在することがあるとします。

```
Import-DSCResource -Module nx 

Node $node {
nxFile DirectoryExample
{
   Ensure = "Present"
   DestinationPath = "/opt/mydir"
   Type = "Directory"
}

nxFile FileExample
{
    Ensure = "Present"
    Destinationpath = "/opt/mydir/myfile"
    Contents=@"
#!/bin/bash`necho "hello world"`n
"@ 
    Mode = “755”
    DependsOn = "[nxFile]DirectoryExample"
} 
}
```





