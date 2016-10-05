---
title: WinRMSecurity
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: b1addddd50368fadcbb2581673d3ebc7cad8e32a

---

# PowerShell リモート処理のセキュリティに関する考慮事項

PowerShell リモート処理は、Windows システムの管理に推奨されている方法です。 Windows Server 2012 R2 では、PowerShell リモート処理が既定で有効になっています。 このドキュメントでは、PowerShell リモート処理を使用する場合のセキュリティ上の問題、推奨事項、およびベスト プラクティスを取り上げています。

## PowerShell リモート処理とは

PowerShell リモート処理は、[Windows リモート管理 (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426.aspx) を使用しています。これは、ユーザーがリモート コンピューター上で PowerShell コマンドを実行できるように、Microsoft によって [Web Services for Management (WS-Management)](http://www.dmtf.org/sites/default/files/standards/documents/DSP0226_1.2.0.pdf) プロトコルが実装されたものです。 PowerShell リモート処理の使用方法の詳細については、「[リモート コマンドの実行](https://technet.microsoft.com/en-us/library/dd819505.aspx)」を参照してください。

PowerShell リモート処理は、コマンドレットの **ComputerName** パラメーターを使用してリモート コンピューターでコマンドを実行することとは異なります。この場合は、基盤となるプロトコルとしてリモート プロシージャ コール (RPC) が使用されています。

##  PowerShell リモート処理の既定の設定

PowerShell リモート処理 (および WinRM) は、次のポートをリッスンします。

- HTTP: 5985
- HTTPS: 5986

既定では、PowerShell リモート処理で許可されている接続は、Administrators グループのメンバーからのもののみです。 セッションはユーザーのコンテキストで開始されるため、PowerShell リモート処理を介して接続されている間は、個々のユーザーとグループに適用されているオペレーティング システムのアクセス制御がすべて、そのセッションに引き続き適用されます。

プライベート ネットワークの場合、PowerShell リモート処理の既定の Windows ファイアウォール規則はすべての接続を受け入れます。 パブリック ネットワークの場合、既定の Windows ファイアウォール規則で許可されるのは、同じサブネット内からの PowerShell リモート処理接続のみです。 パブリック ネットワーク上のすべての接続に対して PowerShell リモート処理を可能にするには、その規則を明示的に変更する必要があります。

>**警告:** パブリック ネットワークのファイアウォール規則は、悪意のある外部接続からコンピューターを保護することを目的としています。 この規則を削除する際は注意してください。

## プロセスの分離

PowerShell リモート処理では、コンピューター間の通信に [Windows リモート管理 (WinRM)](https://msdn.microsoft.com/en-us/library/windows/desktop/aa384426) を使用します。 WinRM は Network Service アカウントでサービスとして実行されており、ユーザー アカウントとして実行される分離プロセスを生成して、PowerShell インスタンスをホストします。 1 ユーザーとして実行されている PowerShell のインスタンスは、別のユーザーとして PowerShell のインスタンスを実行しているプロセスにアクセスすることはできません。

## PowerShell リモート処理によって生成されたイベント ログ

FireEye は、PowerShell リモート処理セッションによって生成されたイベント ログやその他のセキュリティ証拠をまとめたものを提供しています。これは、概要をまとめられています。これについては、  
[Investigating PowerShell Attacks (PowerShell 攻撃の調査)」を参照してください](https://www.fireeye.com/content/dam/fireeye-www/global/en/solutions/pdfs/wp-lazanciyan-investigating-powershell-attacks.pdf)。

## 暗号化とトランスポート プロトコル

PowerShell リモート処理の接続のセキュリティについては、初期認証と進行中の通信という 2 つの観点から考慮することをお勧めします。 

使用されているトランスポート プロトコル (HTTP または HTTPS) に関係なく、PowerShell リモート処理では、常にすべての通信が、初期認証後に、セッションごとの AES 256 対称キーを使用して暗号化されます。
    
### 初期認証

認証では、サーバーに対するクライアントの ID と、理想的にはクライアントに対するサーバーを確認します。
    
クライアントがコンピューター名 (server01 や server01.contoso.com など) を使用してドメイン サーバーに接続する場合、既定の認証プロトコルは [Kerberos](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378747.aspx) です。
Kerberos では、再利用可能な資格情報を送信しなくても、ユーザー ID とサーバー ID の両方が保証されます。

クライアントが IP アドレスを使用してドメイン サーバーに接続する場合、またはワークグループ サーバーに接続する場合は、Kerberos 認証を使用できません。 その場合は、PowerShell リモート処理は [NTLM 認証プロトコル](https://msdn.microsoft.com/en-us/library/windows/desktop/aa378749.aspx)を利用します。 NTLM 認証プロトコルでは、委任可能な資格情報を送信しなくてもユーザー ID が保証されます。 NTLM プロトコルは、ユーザー ID を証明するために、クライアントとサーバーの両方でユーザーのパスワードからセッション キーを計算することを要求します。パスワード自体は交換しません。 サーバーは、通常ユーザーのパスワードを知らないため、ドメイン コントローラーと通信します。ドメイン コントローラーがユーザーのパスワードを知っていて、サーバー用にセッション キーを計算します。 
      
一方、NTLM プロトコルでは、サーバー ID が保証されません。 ドメインに参加しているコンピューターのコンピューター アカウントにアクセスできる攻撃者は、認証に NTLM を使用するプロトコルと同様にドメイン コントローラーを呼び出して NTLM セッション キーを計算することで、サーバーになりすます場合があります。

NTLM ベースの認証は既定では無効になっていますが、ターゲット サーバーで SSL を構成するか、クライアントで WinRM TrustedHosts 設定を構成することで許可できます。
    
#### NTLM ベースの接続時に SSL 証明書を使用してサーバー ID を検証する

NTLM 認証プロトコルではターゲット サーバーの ID を保証できないため (パスワードを認識しているだけのため)、PowerShell リモート処理に SSL を使用するようターゲット サーバーを構成できます。 SSL 証明書をターゲット サーバーに割り当てると (クライアントも信頼している証明機関によって発行されている場合)、ユーザー ID とサーバー ID の両方が保証される NTLM ベースの認証が可能になります。
    
#### NTLM ベースのサーバー ID を無視する
      
SSL 証明書を NTLM 接続用のサーバーに展開できない場合は、そのサーバーを WinRM の **TrustedHosts** 一覧に追加することで ID エラーの発生を抑制できます。 ホスト自体の信頼性を示すために、サーバー名を TrustedHosts の一覧に追加することは考えないでください。NTLM 認証プロトコルでは、実際に接続しているホストが、必ずしも意図して接続しているホストであるとは限りません。
代わりに、TrustedHosts の設定を、サーバーの ID を確認できないことが原因で生成されたエラーを抑制するホストの一覧と考える必要があります。
    
    
### 進行中の通信

初期認証が完了すると、[PowerShell リモート処理プロトコル](https://msdn.microsoft.com/en-us/library/dd357801.aspx)によって、進行中の通信はすべて、セッションごとの AES 256 対称キーを使用して暗号化されます。  


## 次ホップの実行

PowerShell リモート処理では、既定で、認証に Kerberos (使用可能な場合) または NTLM が使用されます。 このどちらのプロトコルも、資格情報を送信することなく、リモート コンピューターに対して認証を行います。
これは認証方法としては最も安全ですが、リモート コンピューターにユーザーの資格情報がないため、このリモート コンピューターは、ユーザーに代わって他のコンピューターおよびサービスにアクセスすることはできません。 これは "ダブルホップ" 問題と呼ばれます。

この問題を回避する方法はいくつかあります。

### Kerberos の制約付き委任

信頼性の高いサーバーの場合は、[Kerberos の制約付き委任](https://technet.microsoft.com/en-us/library/cc995228.aspx)を有効にすることができます。 これにより、リモート サーバーが、指定したコンピューターおよびサービス一覧に対して認証済みユーザーを偽装できるようになります。

### リモート コンピューター間の信頼

*Server2* のリソースについて *Server1* にリモート接続されたユーザーを信頼する場合、そのリソースへのアクセスを *Server1* に明示的に許可できます。

### リモート リソースにアクセスする際に明示的な資格情報を使用する

資格情報をリモート リソースに明示的に渡すには、コマンドレットの **Credential** パラメーターを使用します。 たとえば、次のように入力します。

```powershell
$myCredential = Get-Credential
New-PSDrive -Name Tools \\Server2\Shared\Tools -Credential $myCredential 
```

### CredSSP

認証に[資格情報のセキュリティ サポート プロバイダー (CredSSP)](https://msdn.microsoft.com/en-us/library/windows/desktop/bb931352.aspx) を使用するには、[New-PSSession](https://technet.microsoft.com/en-us/library/hh849717.aspx) コマンドレットの呼び出しの `Authentication` パラメーター値として "CredSSP" を指定します。 CredSSP は資格情報をプレーン テキストでサーバーに渡すため、これを使用すると資格情報の盗難攻撃にさらされます。 リモート コンピューターが侵害されると、攻撃者はユーザーの資格情報にアクセスできます。 CredSSP は、既定では、クライアント コンピューターとサーバー コンピューターの両方で無効になっています。 CredSSP は、最も信頼性の高い環境でのみ有効にしてください。 たとえば、ドメイン コントローラーは信頼性が高いため、ドメイン コントローラーに接続しているドメイン管理者が有効にすることをお勧めします。

PowerShell リモート処理で CredSSP を使用する場合のセキュリティに関する注意事項の詳細については、「[Accidental Sabotage: Beware of CredSSP (予想外の妨害行為: CredSSP に関する注意事項)](http://www.powershellmagazine.com/2014/03/06/accidental-sabotage-beware-of-credssp)」を参照してください。

資格情報の盗難攻撃の詳細については、「[Mitigating Pass-the-Hash (PtH) Attacks and Other Credential Theft (Pass-the-Hash (PtH) 攻撃とその他の資格情報の盗難の抑制)](https://www.microsoft.com/en-us/download/details.aspx?id=36036)」を参照してください。











<!--HONumber=Oct16_HO1-->


