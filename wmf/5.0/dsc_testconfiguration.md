# Test-DscConfiguration コマンドレットでの参照構成のサポート

Test-DscConfiguration コマンドレットが更新されて、参照構成ドキュメントを指定することにより、1 つ以上のターゲット ノードを必要な構成の状態と比較してテストできるようになりました。

次の新しいパラメーター セットでは、指定されたパスにある DSC 構成をテストのみに使い、指定されたターゲット ノード上の各構成に適用することはしません。 Start-DscConfiguration や他の DSC コマンドレットと同様、各 MOF の名前を使って、構成をテストするターゲット ノードが決まります。 

```PowerShell
Test-DscConfiguration   [-Path] <string> 
                        [[-ComputerName] <string[]>] 
                        [-Credential <pscredential>] 
                        [-ThrottleLimit <int>] 
                        [-AsJob] 
                        [<CommonParameters>]

Test-DscConfiguration   [-Path] <string> 
                        -CimSession <CimSession[]> 
                        [-ThrottleLimit <int>] 
                        [-AsJob] 
                        [<CommonParameters>]
```

次の新しいパラメーター セットでは、1 つの DSC 構成をテストのみに使い、指定されたターゲット ノード上の構成に適用することはしません。 

```PowerShell
Test-DscConfiguration   -ReferenceConfiguration <string> 
                        [[-ComputerName] <string[]>]
                        [-Credential <pscredential>] 
                        [-ThrottleLimit <int>] 
                        [-AsJob] 
                        [<CommonParameters>]

Test-DscConfiguration   -ReferenceConfiguration <string> 
                        -CimSession <CimSession[]> 
                        [-ThrottleLimit <int>] 
                        [-AsJob] 
                        [<CommonParameters>]
```


<!--HONumber=Oct16_HO1-->


