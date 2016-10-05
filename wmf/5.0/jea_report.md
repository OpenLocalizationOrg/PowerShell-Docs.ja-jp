# JEA のレポート
JEA 構成の状態をレポートするには、次のコマンドを使用できます。
1.  **Get-PSSessionConfiguration** は、特定のコンピューター上のすべての登録済みエンドポイントの一覧を返します。
2.  **Get-PSSessionCapability** は、特定のユーザーが特定のエンドポイントで持つ機能についてレポートします。

次に、**Get-PSSessionCapability** の例を示します。
```powershell
Get-PSSessionCapability -ConfigurationName Maintenance -Username "CONTOSO\JohnDoe"

CommandType     Name                                               Version    Source           
-----------     ----                                               -------    ------           
Alias           clear -> Clear-Host                                                            
Alias           cls -> Clear-Host                                                              
Alias           exsn -> Exit-PSSession                                                         
Alias           gcm -> Get-Command                                                             
Alias           measure -> Measure-Object                                                      
Alias           select -> Select-Object                                                        
Function        Clear-Host                                                                     
Function        Exit-PSSession                                                                 
Function        Get-Command                                                                    
Function        Get-FormatData                                                                 
Function        Get-Help                                                                       
Function        Get-UserInfo                                                                   
Function        Measure-Object                                                                 
Function        Out-Default                                                                    
Function        Select-Object                                                                  
Cmdlet          Restart-Service                                    3.0.0.0 Microsof...


```

JEA セッション中にユーザーが行った_操作_についてレポートするには、次のことを行います。
1. その JEA エンドポイントの "over-the-shoulder" トランスクリプトを有効にして、トランスクリプト ディレクトリで各ユーザーの操作の完全なログを参照します。
2. PowerShell モジュールのログを有効にし、PowerShell イベント ログを調べます。

<!--HONumber=Oct16_HO1-->


