#DSC Linux nxFileLine リソース用

**NxFileLine** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux ノード上の構成ファイル内の行を管理するメカニズムを提供します。

##構文

```
nxFileLine <string> #ResourceName
{
    FilePath = <string>
    ContainsLine = <string>
    [ DoesNotContainPattern = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| ファイル パス| ターゲット ノード上での行を管理するファイルの完全パス。|
| ContainsLine| 確認するための行は、ファイルに存在します。ファイルにも存在しない場合、この行がファイルに追加されます。**ContainsLine** は必須では、空の文字列に設定することができますが、('ContainsLine = ' ') は必要ない場合。|
| DoesNotContainPattern| ファイル内に存在する必要がある行の正規表現パターン。ファイル内に存在したこの正規表現に一致する行、ファイルから、行は削除します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

この例では、使用方法を示します、 **nxFileLine** を構成するのにはリソース、 `など/sudoers` ことを確認する、ユーザーのファイル: monuser がいない requiretty に構成されています。

```
Import-DSCResource -Module nx 

nxFileLine DoNotRequireTTY
{
   FilePath = “/etc/sudoers”
   ContainsLine = 'Defaults:monuser !requiretty'
   DoesNotContainPattern = "Defaults:monuser[ ]+requiretty"
} 
```





