# スクリプトのトレースとログ

Windows PowerShell には、コマンドレットの呼び出しをログに記録する **LogPipelineExecutionDetails** グループ ポリシー設定が既に備えられていますが、PowerShell のスクリプト言語には、ログへの記録や監査の対象とする可能性がある機能が数多く含まれています。 新しい詳細スクリプト トレース機能では、システムで使用される Windows PowerShell スクリプトの詳細な追跡や分析を有効にすることができます。 詳細なスクリプト トレースを有効にすると、Windows PowerShell はすべてのスクリプト ブロックを ETW イベント ログ **Microsoft-Windows-PowerShell/Operational** に記録します。 スクリプト ブロックによって別のスクリプト ブロックが作成される場合 (たとえば、文字列で Invoke-Expression コマンドレットを呼び出すスクリプト)、その結果として得られるスクリプト ブロックも記録されます。

これらのイベントのログ記録は、**[PowerShell スクリプト ブロックのログ記録を有効にする]** グループ ポリシー設定 ([管理用テンプレート] -> [Windows コンポーネント] -> [Windows PowerShell]) で有効にすることができます。

イベントは次のとおりです。

| ［チャネル］ | Operational                                 |
|---------|---------------------------------------------|
| レベル   | Verbose                                     |
| オペコード  | 作成                                      |
| タスク    | CommandStart                                |
| キーワード | Runspace                                    |
| イベント ID | Engine_ScriptBlockCompiled (0x1008 = 4104)  |
| メッセージ | Creating Scriptblock text (%1 of %2): </br> %3 </br> ScriptBlock ID: %4 |


メッセージに埋め込まれたテキストは、コンパイルされたスクリプト ブロックの範囲です。 ID は、スクリプト ブロックの有効期間に保持される GUID です。

詳細ログ記録の機能を有効にすると、次のように開始マーカーと終了マーカーが書き込まれます。

| ［チャネル］ | Operational                                            |
|---------|--------------------------------------------------------|
| レベル   | Verbose                                                |
| オペコード  | Open (/ Close)                                         |
| タスク    | CommandStart (/ CommandStop)                           |
| キーワード | Runspace                                               |
| イベント ID | ScriptBlock\_Invoke\_Start\_Detail (0x1009 = 4105) / </br> ScriptBlock\_Invoke\_Complete\_Detail (0x100A = 4106) |
| メッセージ | Started (/ Completed) invocation of ScriptBlock ID: %1 </br> Runspace ID: %2 |

ID は (イベント ID 0x1008 に関連付けることができる) スクリプト ブロックを表す GUIDで、Runspace ID はこのスクリプト ブロックが実行された実行空間を表します。

呼び出しメッセージ内のパーセント記号は、構造化 ETW プロパティを表します。 これらはメッセージ テキストで実際の値に置き換えられますが、Get-WinEvent コマンドレットを使用してメッセージを取得し、メッセージの **Properties** 配列を使用すると、より信頼性の高い方法でそれらにアクセスできます。

この機能を利用して、スクリプトを暗号化および難読化する悪意のある試みをラップ解除する方法の例を次に示します。

```powershell
## Malware
function SuperDecrypt
{
    param($script)
    $bytes = [Convert]::FromBase64String($script)
             
    ## XOR “encryption”
    $xorKey = 0x42
    for($counter = 0; $counter -lt $bytes.Length; $counter++)
    {
        $bytes[$counter] = $bytes[$counter] -bxor $xorKey
    }
    [System.Text.Encoding]::Unicode.GetString($bytes)
}

$decrypted = SuperDecrypt "FUIwQitCNkInQm9CCkItQjFCNkJiQmVCEkI1QixCJkJlQg=="
Invoke-Expression $decrypted
```

このことを実行すると、次のログ エントリが生成されます。

```
Compiling Scriptblock text (1 of 1):
function SuperDecrypt
{
    param($script)
    $bytes = [Convert]::FromBase64String($script)
    ## XOR "encryption"
    $xorKey = 0x42
    for($counter = 0; $counter -lt $bytes.Length; $counter++)
    {
        $bytes[$counter] = $bytes[$counter] -bxor $xorKey
    }
    [System.Text.Encoding]::Unicode.GetString($bytes)

}
ScriptBlock ID: ad8ae740-1f33-42aa-8dfc-1314411877e3

Compiling Scriptblock text (1 of 1):
$decrypted = SuperDecrypt "FUIwQitCNkInQm9CCkItQjFCNkJiQmVCEkI1QixCJkJlQg=="
ScriptBlock ID: ba11c155-d34c-4004-88e3-6502ecb50f52

Compiling Scriptblock text (1 of 1):
Invoke-Expression $decrypted
ScriptBlock ID: 856c01ca-85d7-4989-b47f-e6a09ee4eeb3

Compiling Scriptblock text (1 of 1):
Write-Host 'Pwnd'
ScriptBlock ID: 5e618414-4e77-48e3-8f65-9a863f54b4c8
```

スクリプト ブロックの長さが、ETW で単一のイベントに格納できる長さを超えた場合、Windows PowerShell はスクリプトを複数の部分に分割します。 ログ メッセージからスクリプトを再結合するサンプル コードを次に示します。

```powershell
$created = Get-WinEvent -FilterHashtable @{ ProviderName="Microsoft-Windows-PowerShell"; Id = 4104 } | Where-Object { $_.<...> }
$sortedScripts = $created | sort { $_.Properties[0].Value }
$mergedScript = -join ($sortedScripts | % { $_.Properties[2].Value })
```

保持するバッファーが限られているすべてのログ システム (ETW ログなど) の場合と同様、このインフラストラクチャに対して、正しくないイベントでログを溢れさせて以前の証拠を隠す攻撃が行われる場合があります。 この攻撃から保護するには、何らかの形式のイベント ログ収集が設定されていることを確認し (つまり、Windows イベント転送、「[Spotting the Adversary with Windows Event Log Monitoring (Windows イベント ログ監視による敵対者の偵察)](http://www.nsa.gov/ia/_files/app/Spotting_the_Adversary_with_Windows_Event_Log_Monitoring.pdf)」)、できるだけ早くコンピューターからイベント ログを移動します。


<!--HONumber=Oct16_HO1-->


