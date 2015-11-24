#DSC Linux nxSshAuthorizedKeys リソース用

**NxAuthorizedKeys** リソース PowerShell 必要な状態 Configuration (DSC) を管理するメカニズム承認 ssh 指定されたユーザーのキーを提供します。

##構文

```
nxAuthorizedKeys <string> #ResourceName
{
    KeyComment = <string>
    [ Ensure = <string> { Absent | Present }  ]
    [ Username = <string> ]
    [ Key = <string> ]
    [ DependsOn = <string[]> ]

}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| KeyComment| キーの一意のコメントです。これについては、キーを一意に識別するために使用されます。|
| 確認します。| キーが定義されているかどうかを指定します。「ない」、キーが、ユーザーの承認済みキー ファイルには存在しないことを確認するには、このプロパティを設定します。ユーザーの承認済みキー ファイルで、キーが定義されていることを確認するには、"Present"に設定します。|
| ユーザー名| Ssh を管理するユーザー名には、キーが承認されています。定義されていない場合は、既定のユーザーは"root"です。|
| キー| キーの内容です。これは、必要な場合 **を確認してください** "Present"に設定されています。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。たとえば場合、 **ID** を実行する構成スクリプトのブロックの最初は、リソースの **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

次の例では、パブリック ssh 承認されたユーザー"monuser"のキー。

```
Import-DSCResource -Module nx 

Node $node {

nxSshAuthorizedKeys myKey{
   KeyComment = "myKey"
   Ensure = "Present"
   Key = 'ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEA0b+0xSd07QXRifm3FXj7Pn/DblA6QI5VAkDm6OivFzj3U6qGD1VJ6AAxWPCyMl/qhtpRtxZJDu/TxD8AyZNgc8aN2CljN1hOMbBRvH2q5QPf/nCnnJRaGsrxIqZjyZdYo9ZEEzjZUuMDM5HI1LA9B99k/K6PK2Bc1NLivpu7nbtVG2tLOQs+GefsnHuetsRMwo/+c3LtwYm9M0XfkGjYVCLO4CoFuSQpvX6AB3TedUy6NZ0iuxC0kRGg1rIQTwSRcw+McLhslF0drs33fw6tYdzlLBnnzimShMuiDWiT37WqCRovRGYrGCaEFGTG2e0CN8Co8nryXkyWc6NSDNpMzw== rsa-key-20150401'
   UserName = "monuser"
} 
}
```





