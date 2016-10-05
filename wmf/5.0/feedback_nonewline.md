# NoNewLine パラメーター
**Out-File**、**Add-Content**、および **Set-Content** に新しい **–NoNewline** スイッチが追加されました。これを指定すると、出力後の新しい行が省略されます。
```PowerShell
PS C:\> "This is " | Out-File -FilePath Example.txt -NoNewline

PS C:\>; "a single " | Add-Content -Path Example.txt -NoNewline

PS C:\> "sentence." | Add-Content -Path Example.txt -NoNewline

PS C:\> Get-Content .\Example.txt

This is a single sentence.
```
**–NoNewline** を指定しないと、各フラグメントが別個の行に出力されます。
```PowerShell
PS C:\> "This is " | Out-File -FilePath Example.txt

PS C:\> "a single " | Add-Content -Path Example.txt

PS C:\> "sentence." | Add-Content -Path Example.txt

PS C:\> Get-Content .\Example.txt

This is

a single

sentence.
```


<!--HONumber=Oct16_HO1-->


