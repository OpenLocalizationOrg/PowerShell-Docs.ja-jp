# 情報ストリーム

新しい構造化された情報ストリームは、スクリプトとそのホストの間で構造化データを転送するために利用できます。 **Write-Host** が更新され、情報ストリームに送った出力をキャプチャしたり、サイレント状態にしたりできるようになりました。 新しい **Write-Information** コマンドレット (およびそれに対応する WriteInformation API) を共通パラメーター **-InformationVariable** および **-InformationAction** と一緒に使うと、柔軟性と機能が向上します。

```powershell
PS C:\Users\demo> ## Here's a typical PowerShell script
PS C:\Users\demo> Show-ColorizedContent c:\temp\OutputGusher.ps1

001 | [CmdletBinding()]
002 | param()
003 |
004 | Write-Host -ForegroundColor Green "Preparing to give you output!"
005 | Write-Host "============================="
006 | Write-Host "I " -NoNewLine -ForegroundColor White
007 | Write-Host "<3" -ForegroundColor Red –NoNewLine
008 | Write-Host " Output" -ForegroundColor White
009 | Write-Host "============================="
010 |
011 | $p = Get-Process -id $pid
012 | $p
013 |
014 | Write-Information $p -Tag Process
015 | Write-Information "Some spammy logging information" -Tag LogLow
016 | Write-Information "Some important logging information" -Tag LogHigh
017 |
018 | Write-Host
019 | Write-Host -ForegroundColor Green "SCRIPT COMPLETE!One!Eleven!"
020 | Write-Host "============================="

PS C:\Users\demo> ## And a typical problem
PS C:\Users\demo> $r = c:\temp\OutputGusher
Preparing to give you output!
=============================
I <3 Output
=============================
SCRIPT COMPLETE!One!Eleven!
=============================
PS C:\Users\demo>
PS C:\Users\demo> ## Output still has some captured data
PS C:\Users\demo> $r.Id
4008
PS C:\Users\demo>
PS C:\Users\demo> ## With the -InformationVariable common variable, you can capture
PS C:\Users\demo> ## the Write-Host data now.
PS C:\Users\demo> $r = c:\temp\OutputGusher -InformationVariable iv
Preparing to give you output!
=============================
I <3 Output
=============================
SCRIPT COMPLETE!One!Eleven!
=============================
PS C:\Users\demo> $ivOutput = $iv | Group-Object -AsHash { $_.Tags[0] } -AsString
PS C:\Users\demo> $ivOutput.PSHOST | Format-Table

MessageData
-----------

{[Message, Preparing to give you output!], [NoNewLine, False], [ForegroundColor, Green], [BackgroundColor, DarkMagenta]}
{[Message, =============================], [NoNewLine, False], [ForegroundColor, DarkYellow], [BackgroundColor, DarkMa...
{[Message, I ], [NoNewLine, True], [ForegroundColor, White], [BackgroundColor, DarkMagenta]}
{[Message, <3], [NoNewLine, True], [ForegroundColor, Red], [BackgroundColor, DarkMagenta]}
{[Message,  Output], [NoNewLine, False], [ForegroundColor, White], [BackgroundColor, DarkMagenta]}
{[Message, =============================], [NoNewLine, False], [ForegroundColor, DarkYellow], [BackgroundColor, DarkMa...
{[Message, ], [NoNewLine, False], [ForegroundColor, DarkYellow], [BackgroundColor, DarkMagenta]}
{[Message, SCRIPT COMPLETE!One!Eleven!], [NoNewLine, False], [ForegroundColor, Green], [BackgroundColor, DarkMagenta]}
{[Message, =============================], \[NoNewLine, False], [ForegroundColor, DarkYellow], [BackgroundColor, DarkMa...

PS C:\Users\demo>

PS C:\Users\demo> ## And even ignore spammy output altogether
PS C:\Users\demo> $r = c:\temp\OutputGusher -InformationAction "SilentlyContinue"
PS C:\Users\demo>

PS C:\Users\demo> ## As we saw in lines 14..16 of the original script, though,
PS C:\Users\demo> ## the Write-Information cmdlet lets a script emit more than

PS C:\Users\demo> ## just one stream of output.
PS C:\Users\demo> $ivOutput.LogHigh | % { Write-Warning $_.MessageData }
WARNING: Some important logging information
PS C:\Users\demo>
PS C:\Users\demo> ## All output includes useful properties that you would expect
PS C:\Users\demo> ## of a generic event stream
PS C:\Users\demo> $ivOutput.Process 
MessageData     : System.Diagnostics.Process (powershell)
Source          : c:\temp\OutputGusher.ps1
TimeGenerated   : 2/9/2015 5:08:52 PM
Tags            : {Process}
User            : demo
Computer        : srv2
ProcessId       : 4008
NativeThreadId  : 7980
ManagedThreadId : 8

PS C:\Users\demo> ## And of course, this works from hosting applications.
PS C:\Users\demo> $ps = [PowerShell]::Create()
PS C:\Users\demo> $ps.AddCommand('c:\temp\OutputGusher.ps1').Invoke() 

Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id ProcessName
-------  ------    -----      ----- -----   ------     -- -----------
    559      41   119704     130968 ...46     7.77   4008 powershell

PS C:\Users\demo> $information = $ps.Streams.Information
PS C:\Users\demo> $information | ? { $_.Tags -contains 'LogLow'}

MessageData     : Some spammy logging information
Source          : C:\temp\OutputGusher.ps1
TimeGenerated   : 2/9/2015 5:08:53 PM
Tags            : {LogLow}
User            : demo
Computer        : srv2
ProcessId       : 4008
NativeThreadId  : 2276
ManagedThreadId : 14
```


<!--HONumber=Oct16_HO1-->


