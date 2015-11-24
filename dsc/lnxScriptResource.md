#DSC Linux nxScript リソース用

**NxScript** リソース PowerShell 必要な状態 Configuration (DSC) では、Linux のノード上の Linux のスクリプトを実行するメカニズムを提供します。

##構文

```
nxScript <string> #ResourceName
{
    GetScript = <string>
    SetScript = <string>
    TestScript = <string>
    [ User = <string> ]
    { Group = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| GetScript| 呼び出すときに実行されるスクリプトを提供する、 [Get DscConfiguration](https://technet.microsoft.com/en-us/library/dn521625.aspx) コマンドレットです。スクリプトは、# など、shebang を始める必要があります。/、ビン分割/bash します。|
| SetScript| スクリプトを提供します。呼び出した場合、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレット、 **TestScript** は最初に実行します。場合、 **TestScript** ブロックには、0 以外の終了コードが返された、 **SetScript** ブロックが実行されます。場合、 **TestScript** 終了コードは 0 を返す、 **SetScript** は実行されません。スクリプトがなどの shebang で始まる必要があります `#!/、ビン分割/bash`です。|
| TestScript| スクリプトを提供します。呼び出した場合、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットでは、このスクリプトを実行します。0 以外の終了コードが返された場合、SetScript が実行されます。0 の場合、終了コードが返された場合、 **SetScript** は実行されません。 **TestScript** を起動するときにも実行、 [テスト DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) コマンドレットです。ただし、ここでは、 **SetScript** からどのような終了コードが返されるも実行されず、 **TestScript**です。 **TestScript** 実際の構成は、現在の目的の状態の構成を一致し、が一致しない場合、終了コード 0 以外の場合は、終了コードは 0 を返す必要があります。(現在の目的の状態の構成は、前回の構成が DSC を使用しているノードで施行) です。スクリプトは、1#!/bin/bash.1 など、shebang を始める必要があります。|
| ユーザー| としてスクリプトを実行するユーザー。|
| グループ| としてスクリプトを実行するグループです。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

次の例では、使用、 **nxScript** リソースを追加の構成の管理を実行します。

```
Import-DSCResource -Module nx 

Node $node {
nxScript KeepDirEmpty{

    GetScript = @"
#!/bin/bash
ls /tmp/mydir/ | wc -l
"@

    SetScript = @"
#!/bin/bash
rm -rf /tmp/mydir/*
"@

    TestScript = @'
#!/bin/bash
filecount=`ls /tmp/mydir | wc -l`
if [ $filecount -gt 0 ]
then
    exit 1
else
    exit 0
fi
'@
} 
}
```




