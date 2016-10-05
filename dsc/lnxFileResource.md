---
title: "Linux 用 DSC の nxFile リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 2ba44df5dd6c91371cbbfe95d48184a4ff4a7738

---

# Linux 用 DSC の nxFile リソース

PowerShell Desired State Configuration (DSC) の **nxFile** リソースは、Linux ノード上でファイルとディレクトリを管理するためのメカニズムを備えています。

## 構文

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

## プロパティ

|  プロパティ |  説明 | 
|---|---|
| DestinationPath| ファイルまたはディレクトリの状態を保証する場所を指定します。| 
| SourcePath| ファイルまたはフォルダー リソースのコピー元のパスを指定します。 このパスはローカル パスまたは `http/https/ftp` URL です。 リモート `http/https/ftp` URL は、**Type** プロパティの値が file の場合にのみサポートされます。| 
| Ensure| ファイルが存在するかどうかを決定します。 ファイルが存在することを保証するには、このプロパティを "Present" に設定します。 ファイルが存在しないことを保証するには、"Absent" に設定します。 既定値は "Present" です。| 
| 種類| 構成されるリソースがディレクトリまたはファイルのいずれであるかを指定します。 リソースがディレクトリであることを示すには、このプロパティを "directory" に設定します。 リソースがファイルであることを示すには、"file" に設定します。 既定値は "file" です。| 
| 内容| 特定の文字列など、ファイルの内容を指定します。| 
| チェックサム| 2 つのファイルが同じであるかどうかを決定するときに使用するタイプを定義します。 **Checksum** が指定されていない場合、ファイルまたはディレクトリ名のみが比較に使用されます。 値は、"ctime"、"mtime"、または "md5" です。| 
| Recurse| サブディレクトリが含まれるかどうかを示します。 サブディレクトリを含めるようにするには、このプロパティを **$true** に設定します。 既定値は **$false** です。 **注:** このプロパティは、**Type** プロパティが directory に設定されている場合にのみ有効です。| 
| Force| 特定のファイル操作 (ファイルの上書き、空でないディレクトリの削除など) によって、エラーが発生します。 **Force** プロパティを使用すると、このようなエラーが上書きされます。 既定値は **$false** です。| 
| リンク| シンボリック リンクの目的の動作を指定します。 シンボリック リンクに従い、処理の対象をリンクのターゲットにするには、このプロパティを "follow" に設定します (たとえば、 リンクではなくファイルをコピーします)。 処理の対象をリンクにするには、このプロパティを "manage" に設定します (たとえば、 リンク自体をコピーします)。 シンボリック リンクを無視するには、このプロパティを "ignore" に設定します。| 
| グループ| ファイルまたはディレクトリを所有する**グループ**の名前。| 
| モード| 8 進数またはシンボリック表記で、リソースに必要なアクセス許可を指定します。 (たとえば、777 または rwxrwxrwx)。 シンボリック表記を使用する場合は、ディレクトリまたはファイルを示す最初の文字を指定しないでください。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの **ID** が **ResourceName** で、そのタイプが **ResourceType** である場合、このプロパティを使用する構文は `DependsOn = "[ResourceType]ResourceName"` になります。| 

## 追加情報


Linux と Windows の既定では、テキスト ファイルで異なる改行文字を使用して、これにより、__nxFile__ を使用して Linux コンピューターで一部のファイルを構成すると、予期しない結果が発生することがあります。 予期しない改行文字によって引き起こされる問題を回避しながら Linux ファイルのコンテンツを管理する複数の方法があります。

手順 1: リモート ソース (http、https、または ftp) からファイルをコピーします。目的のコンテンツを含むファイルを Linux 上で作成し、構成するノードにアクセスできる Web サーバーまたは FTP サーバーでステージングします。 ファイルへの Web または FTP の URL で、__nxFile__ リソースの __SourcePath__ プロパティを定義します。

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


手順 2: Linux 改行文字を使用する __$OFS__ プロパティの設定後に、[Get-Content](https://technet.microsoft.com/en-us/library/hh849787.aspx) を使用して PowerShell スクリプトのファイル コンテンツを読み取ります。


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


手順 3: PowerShell 関数を使用して、Linux 改行文字で Windows の改行を置き換えます。

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

## 例

次の例では、ディレクトリ `/opt/mydir` が存在し、指定されたコンテンツを持つファイルがこのディレクトリが存在するようにしています。

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




<!--HONumber=Oct16_HO1-->


