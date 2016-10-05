# Cryptographic Message Syntax (CMS) コマンドレット

Cryptographic Message Syntax コマンドレットは、[RFC5652](http://tools.ietf.org/html/rfc5652) で説明されているように暗号によってメッセージを保護するための IETF 標準書式を使用して、コンテンツの暗号化と暗号化解除をサポートします。

```powershell
Get-CmsMessage [-Content] <string>
Get-CmsMessage [-Path] <string>
Get-CmsMessage [-LiteralPath] <string>
Protect-CmsMessage [-To] <CmsMessageRecipient[]> [-Content] <string> [[-OutFile] <string>]
Protect-CmsMessage [-To] <CmsMessageRecipient[]> [-Path] <string> [[-OutFile] <string>]
Protect-CmsMessage [-To] <CmsMessageRecipient[]> [-LiteralPath] <string> [[-OutFile] <string>]
Unprotect-CmsMessage [-EventLogRecord] <EventLogRecord> [[-To] <CmsMessageRecipient[]>] [-IncludeContext]
Unprotect-CmsMessage [-Content] <string> [[-To] <CmsMessageRecipient[]>] [-IncludeContext]
Unprotect-CmsMessage [-Path] <string> [[-To] <CmsMessageRecipient[]>] [-IncludeContext]
Unprotect-CmsMessage [-LiteralPath] <string> [[-To] <CmsMessageRecipient[]>] [-IncludeContext]
```

CMS 暗号化標準では、公開キー暗号化が実装されます。公開キー暗号化では、コンテンツの暗号化に使用されるキー (*公開キー*) とコンテンツの暗号化解除に使用されるキー (*秘密キー*) が区別されます。

公開キーは広く共有でき、機密性の高いデータではありません。 いずれかのコンテンツがこの公開キーで暗号化された場合、秘密キーのみが暗号化を解除できます。 公開キー暗号化の詳細については、<http://en.wikipedia.org/wiki/Public-key_cryptography> をご覧ください。

PowerShell で認識されるためには、暗号化証明書に、データ暗号化証明書として識別するための ('Code Signing'、'Encrypted Mail' の識別子のような) 一意のキー使用法識別子 (EKU) が必要です。

ドキュメントの暗号化用の証明書を作成する例を次に示します。

```powershell
(Change the text in **Subject** to your name, email, or other identifier), and put in a file (i.e.: DocumentEncryption.inf):
[Version]
Signature = "$Windows NT$"
[Strings]
szOID\_ENHANCED\_KEY\_USAGE = "2.5.29.37"
szOID\_DOCUMENT\_ENCRYPTION = "1.3.6.1.4.1.311.80.1"
[NewRequest]
Subject = "<cn=me@somewhere.com>"
MachineKeySet = false
KeyLength = 2048
KeySpec = AT\_KEYEXCHANGE
HashAlgorithm = Sha1
Exportable = true
RequestType = Cert
KeyUsage = "CERT\_KEY\_ENCIPHERMENT\_KEY\_USAGE | CERT\_DATA\_ENCIPHERMENT\_KEY\_USAGE"
ValidityPeriod = "Years"
ValidityPeriodUnits = "1000"
[Extensions]
%szOID\_ENHANCED\_KEY\_USAGE% = "{text}%szOID\_DOCUMENT\_ENCRYPTION%"
```

次に、次のコマンドを実行します。
```powershell
certreq -new DocumentEncryption.inf DocumentEncryption.cer
```

これで、コンテンツを暗号化および暗号化解除できます。

```powershell
$protected = "Hello World" | Protect-CmsMessage -To "\*me@somewhere.com\*[](mailto:*leeholm@microsoft.com*)"
$protected
-----BEGIN CMS-----
MIIBqAYJKoZIhvcNAQcDoIIBmTCCAZUCAQAxggFQMIIBTAIBADA0MCAxHjAcBgNVBAMMFWxlZWhv
bG1AbWljcm9zb2Z0LmNvbQIQQYHsbcXnjIJCtH+OhGmc1DANBgkqhkiG9w0BAQcwAASCAQAnkFHM
proJnFy4geFGfyNmxH3yeoPvwEYzdnsoVqqDPAd8D3wao77z7OhJEXwz9GeFLnxD6djKV/tF4PxR
E27aduKSLbnxfpf/sepZ4fUkuGibnwWFrxGE3B1G26MCenHWjYQiqv+Nq32Gc97qEAERrhLv6S4R
G+2dJEnesW8A+z9QPo+DwYU5FzD0Td0ExrkswVckpLNR6j17Yaags3ltNVmbdEXekhi6Psf2MLMP
TSO79lv2L0KeXFGuPOrdzPAwCkV0vNEqTEBeDnZGrjv/5766bM3GW34FXApod9u+VSFpBnqVOCBA
DVDraA6k+xwBt66cV84OHLkh0kT02SIHMDwGCSqGSIb3DQEHATAdBglghkgBZQMEASoEEJbJaiRl
KMnBoD1dkb/FzSWAEBaL8xkFwCu0e1ZtDj7nSJc=
-----END CMS-----

$protected | Unprotect-CmsMessage
Hello World
```

**CMSMessageRecipient** 型のパラメーターでは、次の形式の識別子がサポートされます。
- (証明書プロバイダーから取得された) 実際の証明書
- 証明書を含むファイルのパス
- 証明書を含むディレクトリのパス
- 証明書の拇印 (証明書ストア内の検索に使用)
- 証明書のサブジェクト名 (証明書ストア内の検索に使用)

証明書プロバイダーでドキュメントの暗号化証明書を表示するには、**-DocumentEncryptionCert** 動的パラメーターを使用できます。

```powershell
dir -DocumentEncryptionCert
```

<!--HONumber=Oct16_HO1-->


