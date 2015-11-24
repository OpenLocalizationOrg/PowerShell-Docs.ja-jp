#MOF ファイルをセキュリティで保護します。

> Windows PowerShell 4.0 では、Windows PowerShell 5.0 の適用対象:

DSC は、ローカルの Configuration Manager (LCM) が必要な構成を実装する各ノードにその情報を含む MOF ファイルを送信することでは、どのような構成を対象のノードに指示します。 このファイルには、構成の詳細が含まれている、ためには、安全に保つ必要があります。 これを行うには、ユーザーの資格情報を確認するのには、ローカル Configuration Manager を設定できます。 このトピックでは、証明書を暗号化したりすることで、ターゲット ノードを安全にこれらの資格情報を送信する方法について説明します。

##前提条件

DSC 構成をセキュリティで保護するために使用する資格情報を正常に暗号化するには、次のようなメッセージがあるを確認します。

* **何らかの発行し、証明書を配布する手段**です。 このトピックとその例については、Active Directory の証明機関を使用するいると仮定します。 Active Directory 証明書サービスの詳細な背景情報を参照してください。 [Active Directory 証明書サービスの概要](https://technet.microsoft.com/library/hh831740.aspx) と [Windows Server 2008 の Active Directory Certificate Services](https://technet.microsoft.com/windowsserver/dd448615.aspx)です。
* **、対象のノードまたはノードへの管理者アクセス**です。
* **対象の各ノードは、証明書が暗号化対応であることを個人用ストアの保存が**です。 Windows PowerShell では、ストアへのパスは、Cert: \LocalMachine\My です。 このトピックの例では、[既定の証明書のテンプレート] (https://technet.microsoft.com/library/cc740061 (v=WS.10).aspx) (およびその他の証明書テンプレートでは) 検索できる [ワークステーション認証] テンプレートを使用します。
* この構成を実行する、ターゲット ノード以外のコンピューターで場合 **、証明書の公開キーをエクスポート**, から構成を実行するコンピューターにインポートするとします。 のみにエクスポートするかどうかを確認、 **パブリック** キーは、セキュリティで保護された秘密キーを保持します。

##全体的なプロセス

1. 証明書、キー、および各ターゲット ノードには、証明書のコピーを構成のコンピューターが、公開キーと拇印を持つことを確認して、拇印を設定します。
1. パスと公開キーの拇印を含む構成データのブロックを作成します。
1. ターゲット ノードの望みどおりの構成を定義し、ローカルの構成のコマンドを実行して、ターゲット ノードでは、復号化を設定します構成スクリプトに、証明書と、その拇印を使用して、構成データの暗号化を解除するマネージャーを作成します。
1. ローカルの Configuration Manager の設定を設定し、DSC 構成を開始すると、構成を実行します。

##構成データ

構成データのブロックでは、操作するために、かどうか、または資格情報、暗号化の方法、およびその他の情報を暗号化しないようにする対象のノードを定義します。 構成データのブロックの詳細については、次を参照してください。 [構成を分離すること、および環境データ](configData.md)です。

この例では、名前付きの targetNode へのパス (targetNode.cer という名前)、公開キー証明書のファイルと公開キーのサムプリントで機能するターゲット ノードを指定する構成データのブロックを使用します。

```powershell
$ConfigData= @{ 
    AllNodes = @(     
            @{  
                # The name of the node we are describing 
                NodeName = "targetNode" 

                # The path to the .cer file containing the 
                # public key of the Encryption Certificate 
                # used to encrypt credentials for this node 
                CertificateFile = "C:\publicKeys\targetNode.cer" 


                # The thumbprint of the Encryption Certificate 
                # used to decrypt the credentials on target node 
                Thumbprint = "AC23EA3A9E291A75757A556D0B71CBBF8C4F6FD8" 
            }; 
        );    
    }
```

##構成スクリプト

構成スクリプトでは、使用して、 `PsCredential` パラメーターをできるだけ短時間の資格情報を保存することを確認します。 指定の例を実行すると、DSC は資格情報を要求して、構成データのブロックの対象ノードに関連付けられている CertificateFile を使用して MOF ファイルを暗号化します。 このコード例では、ユーザーにセキュリティ保護されている共有からファイルをコピーします。

```
configuration CredentialEncryptionExample 
{ 
    param( 
        [Parameter(Mandatory=$true)] 
        [ValidateNotNullorEmpty()] 
        [PsCredential] $credential 
        ) 


    Node $AllNodes.NodeName 
    { 
        File exampleFile 
        { 
            SourcePath = "\\Server\share\path\file.ext" 
            DestinationPath = "C:\destinationPath" 
            Credential = $credential 
        } 
    } 
}
```

##復号化の設定

前に `開始 DscConfiguration` 機能しますが、証明書 Id のリソースを使用して、証明書の拇印を確認する証明書を使用して、資格情報の暗号化を解除する各ターゲット ノード上のローカルの Configuration Manager を指示する必要です。 この例の関数には、(を使用する正確な証明書を検索するためにカスタマイズする必要があります)、適切なローカル証明書があります。

```powershell
# Get the certificate that works for encryption 
function Get-LocalEncryptionCertificateThumbprint 
{ 
    (dir Cert:\LocalMachine\my) | %{
        # Verify the certificate is for Encryption and valid 
        if ($_.PrivateKey.KeyExchangeAlgorithm -and $_.Verify()) 
        { 
            return $_.Thumbprint 
        } 
    } 
}
```

その拇印で識別された証明書が、値を使用する構成スクリプトを更新することができます。

```powershell
configuration CredentialEncryptionExample 
{ 
    param( 
        [Parameter(Mandatory=$true)] 
        [ValidateNotNullorEmpty()] 
        [PsCredential] $credential 
        ) 


    Node $AllNodes.NodeName 
    { 
        File exampleFile 
        { 
            SourcePath = "\\Server\share\path\file.ext" 
            DestinationPath = "C:\destinationPath" 
            Credential = $credential 
        } 

        LocalConfigurationManager 
        { 
             CertificateId = $node.Thumbprint 
        } 
    } 
}
```

##構成を実行しています。

この時点で、構成では、2 つのファイルが出力を実行できます。

* A *. ですが、ローカル コンピューターのストアに格納されているし、その拇印で識別された証明書を使用する資格情報の暗号化を解除するローカルの Configuration Manager を構成する meta.mof ファイルです。 セット DscLocalConfigurationManager の適用、*. meta.mof ファイルです。
* 実際には、構成を適用するための MOF ファイルです。 開始 DscConfiguration には、構成が適用されます。

これらのコマンドでは、これらの手順が実行されます。

```powershell
Write-Host "Generate DSC Configuration..."
CredentialEncryptionExample -ConfigurationData $ConfigData -OutputPath .\CredentialEncryptionExample

Write-Host "Setting up LCM to decrypt credentials..."
Set-DscLocalConfigurationManager .\CredentialEncryptionExample -Verbose 

Write-Host "Starting Configuration..."
Start-DscConfiguration .\CredentialEncryptionExample -wait -Verbose
```

すべての次の手順とエクスポートし、公開キーのコピーをヘルパー コマンドレットが組み込まれている完全な例を次に示します。

```powershell
# A simple example of using credentials
configuration CredentialEncryptionExample
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PsCredential] $credential
        )


    Node $AllNodes.NodeName
    {
        File exampleFile
        {
            SourcePath = "\\server\share\file.txt"
            DestinationPath = "C:\Users\user"
            Credential = $credential
        }

        LocalConfigurationManager
        {
            CertificateId = $node.Thumbprint
        }
    }
}

# A Helper to invoke the configuration, with the correct public key 
# To encrypt the configuration credentials
function Start-CredentialEncryptionExample
{
    [CmdletBinding()]
    param ($computerName)


    [string] $thumbprint = Get-EncryptionCertificate -computerName $computerName -Verbose
    Write-Verbose "using cert: $thumbprint"

    $certificatePath = join-path -Path "$env:SystemDrive\$script:publicKeyFolder" -childPath "$computername.EncryptionCertificate.cer"         

    $ConfigData=    @{
        AllNodes = @(     
                        @{  
                            # The name of the node we are describing
                            NodeName = "$computerName"

                            # The path to the .cer file containing the
                            # public key of the Encryption Certificate
                            CertificateFile = "$certificatePath"

                            # The thumbprint of the Encryption Certificate
                            # used to decrypt the credentials
                            Thumbprint = $thumbprint
                        };
                    );    
    }

    Write-Verbose "Generate DSC Configuration..."
    CredentialEncryptionExample -ConfigurationData $ConfigData -OutputPath .\CredentialEncryptionExample `
        -credential (Get-Credential -UserName "$env:USERDOMAIN\$env:USERNAME" -Message "Enter credentials for configuration") 

    Write-Verbose "Setting up LCM to decrypt credentials..."
    Set-DscLocalConfigurationManager .\CredentialEncryptionExample -Verbose 

    Write-Verbose "Starting Configuration..."
    Start-DscConfiguration .\CredentialEncryptionExample -wait -Verbose

}


#region HelperFunctions

# The folder name for the exported public keys
$script:publicKeyFolder = "publicKeys"

# Get the certificate that works for encryptions
function Get-EncryptionCertificate
{
    [CmdletBinding()]
    param ($computerName)
    $returnValue= Invoke-Command -ComputerName $computerName -ScriptBlock {
            $certificates = dir Cert:\LocalMachine\my

            $certificates | %{
                    # Verify the certificate is for Encryption and valid
                    if ($_.PrivateKey.KeyExchangeAlgorithm -and $_.Verify())
                    {
                        # Create the folder to hold the exported public key
                        $folder= Join-Path -Path $env:SystemDrive\ -ChildPath $using:publicKeyFolder
                        if (! (Test-Path $folder))
                        {
                            md $folder | Out-Null
                        }

                        # Export the public key to a well known location
                        $certPath = Export-Certificate -Cert $_ -FilePath (Join-Path -path $folder -childPath "EncryptionCertificate.cer") 

                        # Return the thumbprint, and exported certificate path
                        return @($_.Thumbprint,$certPath);
                    }
                  }
        }
    Write-Verbose "Identified and exported cert..."
    # Copy the exported certificate locally
    $destinationPath = join-path -Path "$env:SystemDrive\$script:publicKeyFolder" -childPath "$computername.EncryptionCertificate.cer"
    Copy-Item -Path (join-path -path \\$computername -childPath $returnValue[1].FullName.Replace(":","$"))  $destinationPath | Out-Null

    # Return the thumbprint
    return $returnValue[0]
}
```



