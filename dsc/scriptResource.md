#DSC スクリプト リソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

**スクリプト** リソースで Windows PowerShell 必要な状態 Configuration (DSC) が対象のノードで Windows PowerShell スクリプトのブロックを実行するメカニズムを提供します。

##構文

```
Script [string] #ResourceName
{
    GetScript = [string]
    SetScript = [string]
    TestScript = [string]
    [ Credential = [PSCredential] ]
    [ DependsOn = [string[]] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| GetScript| 起動するときに実行される Windows PowerShell スクリプトのブロックを提供、 [Get DscConfiguration](https://technet.microsoft.com/en-us/library/dn407379.aspx) コマンドレットです。このブロックには、ハッシュ テーブルを返す必要があります。|
| SetScript| Windows PowerShell スクリプトのブロックを提供します。呼び出した場合、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレット、 **TestScript** ブロックは最初に実行します。場合、 **TestScript** ブロックを返します。 **$false**, 、 **SetScript** ブロックが実行されます。場合、 **TestScript** ブロックを返します。 **$true**, 、 **SetScript** ブロックは実行されません。|
| TestScript| Windows PowerShell スクリプトのブロックを提供します。呼び出した場合、 [開始 DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットでは、このブロックを実行します。返された場合 **$false**, 、SetScript ブロックが実行されます。返された場合 **$true**, 、ブロックは SetScript を実行されません。 **TestScript** を呼び出すときにもブロックが実行される、 [テスト DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) コマンドレットです。ただし、ここでは、 **SetScript** いないはブロックの実行、TestScript がどのような値に関係なくのブロックを返します。 **TestScript** ブロックは実際の構成と一致する場合、現在の目的の状態の構成と False が一致しない場合は True を返す必要があります。(目的の状態の現在の構成とは、前回の構成が DSC を使用しているノードで施行です)。|
| Credential| 資格情報が必要な場合、このスクリプトを実行するために使用する資格情報を示します。|
| DependsOn| このリソースを構成する前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 **ResourceName** あり、型が **リソースの種類**, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。

##例

```powershell
Script ScriptExample
{
    SetScript = { 
        $sw = New-Object System.IO.StreamWriter("C:\TempFolder\TestFile.txt")
        $sw.WriteLine("Some sample string")
        $sw.Close()
    }
    TestScript = { Test-Path "C:\TempFolder\TestFile.txt" }
    GetScript = { <# This must return a hash table #> }          
}
```





