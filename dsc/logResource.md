#DSC ログのリソース

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

__ログ__ リソースで Windows PowerShell 必要な状態 Configuration (DSC) は、状態の構成と分析の必要な Microsoft の Windows のイベント ログにメッセージを記述するメカニズムを提供します。

```
Syntax

Log [string] #ResourceName
{
    Message = [string]
    [ DependsOn = [string[]] ]
}
```

##プロパティ

| プロパティ| 説明|
|---|---|
| メッセージ| Microsoft-Windows-Desired 状態の構成と分析のイベント ログに書き込むには、目的のメッセージを示します。|
| DependsOn| このログ メッセージを書き込む前に別のリソースの構成を実行する必要があることを示します。リソースの構成の ID はスクリプト ブロックを実行する場合が最初はたとえば、 __ResourceName__ あり、型が __リソースの種類__, 、このプロパティを使用するための構文は `DependsOn ="[リソースの種類] ResourceName"`です。|

##例

次の例では、Microsoft-Windows-Desired 状態の構成と分析は、イベント ログにメッセージを含める方法を示します。

> **注**: を実行する場合 [テスト DscConfiguration](https://technet.microsoft.com/en-us/library/dn407382.aspx) では、このリソースを構成すると、それが常に返す **$false**です。

```powershell 
Log LogExample
{
    Message = "This message will appear in the Microsoft-Windows-Desired State Configuration/Analytic event log."
} 
```





