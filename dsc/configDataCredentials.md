---
title: "構成データでの資格情報オプション"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: a750fb208e73ce2ebffb2fa86a55c825169d8ad8

---

# 構成データでの資格情報オプション
>適用先: Windows PowerShell 5.0

## プレーンテキスト パスワードとドメイン ユーザー

暗号化されていない資格情報を含む DSC 構成では、プレーンテキスト パスワードについてのエラー メッセージが生成されます。
また、DSC では、ドメイン資格情報を使用すると警告が生成されます。
これらのエラーや警告メッセージが表示されないようにするには、次の DSC 構成データ キーワードを使用します。
* **PsDscAllowPlainTextPassword**
* **PsDscAllowDomainUser**

## DSC での資格情報の処理

DSC 構成リソースは、既定で `Local System` として実行されます。
ただし、`Package` リソースでソフトウェアを特定のユーザー アカウントでインストールする必要がある場合など、リソースによっては資格情報が必要となることがあります。

以前のリソースでは、ハード コードされた `Credential` プロパティ名使用してこのことに対処していました。
WMF 5.0 では、すべてのリソースに対して自動 `PsDscRunAsCredential` プロパティが追加されました。 `PsDscRunAsCredential` の使用の詳細については、「[ユーザーの資格情報を指定して DSC を実行する](runAsUser.md)」を参照してください。
新しいリソースおよびカスタム リソースでは、資格情報の独自のプロパティを作成する代わりに、この自動プロパティを使用できます。

*一部のリソースのデザインが特定の理由で複数の資格情報を使用していることに注意してくださいされ、独自の資格情報のプロパティを持ちます。*

リソースの使用可能な資格情報プロパティを検索するには、ISE で `Get-DscResource -Name ResourceName -Syntax` または Intellisense を使用します (`CTRL+SPACE`)。

```PowerShell
PS C:\> Get-DscResource -Name Group -Syntax
Group [String] #ResourceName
{
    GroupName = [string]
    [Credential = [PSCredential]]
    [DependsOn = [string[]]]
    [Description = [string]]
    [Ensure = [string]{ Absent | Present }]
    [Members = [string[]]]
    [MembersToExclude = [string[]]]
    [MembersToInclude = [string[]]]
    [PsDscRunAsCredential = [PSCredential]]
}
```

この例では、`PSDesiredStateConfiguration` 組み込み DSC リソース モジュールの [Group](https://msdn.microsoft.com/en-us/powershell/dsc/groupresource) リソースを使用します。
ローカル グループを作成し、メンバーを追加または削除できます。
`Credential` プロパティと、自動 `PsDscRunAsCredential` プロパティの両方を受け取ります。
ただし、リソースでは `Credential` プロパティのみが使用されます。
`PsDscRunAsCredential` の詳細については、[WMF のリリース ノート](https://msdn.microsoft.com/en-us/powershell/wmf/dsc_runas)を参照してください。

## 例: Group リソース資格情報プロパティ

DSC は `Local System` で実行されるため、ローカル ユーザーおよびグループを変更するためのアクセス許可が既にあります。
追加されたメンバーがローカル アカウントの場合、資格情報は必要ありません。
`Group` リソースがローカル グループにドメイン アカウントを追加する場合、資格情報が必要となります。

Active Directory への匿名クエリは許可されません。
`Group` リソースの `Credential` プロパティは、Active Directory のクエリに使用されるドメイン アカウントです。
既定では、ユーザーは Active Directory 内の大部分のオブジェクトを*読み取る*ことができるため、ほとんどの場合これは汎用ユーザー アカウントです。

## 構成の例

次のコード例では、DSC を使用してローカル グループにドメイン ユーザーを設定します。

```PowerShell
Configuration DomainCredentialExample
{
    param
    (
        [PSCredential] $DomainCredential
    )
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    node localhost
    {
        Group DomainUserToLocalGroup
        {
            GroupName        = 'ApplicationAdmins'
            MembersToInclude = 'contoso\alice'
            Credential       = $DomainCredential
        }
    }
}

$cred = Get-Credential -UserName contoso\genericuser -Message "Password please"
DomainCredentialExample -DomainCredential $cred
```

このコードでは、エラーと警告メッセージの両方が生成されます。

```
ConvertTo-MOFInstance : System.InvalidOperationException error processing
property 'Credential' OF TYPE 'Group': Converting and storing encrypted
passwords as plain text is not recommended. For more information on securing
credentials in MOF file, please refer to MSDN blog:
http://go.microsoft.com/fwlink/?LinkId=393729

At line:11 char:9
+   Group
At line:297 char:16
+     $aliasId = ConvertTo-MOFInstance $keywordName $canonicalizedValue
+                ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (:) [Write-Error], InvalidOperationException
    + FullyQualifiedErrorId : FailToProcessProperty,ConvertTo-MOFInstance

WARNING: It is not recommended to use domain credential for node 'localhost'.
In order to suppress the warning, you can add a property named
'PSDscAllowDomainUser' with a value of $true to your DSC configuration data
for node 'localhost'.
```

この例には、次の 2 つの問題があります。
1.  プレーンテキスト パスワードが推奨されないことを説明するエラー。
2.  ドメイン資格情報を使用しないよう勧める警告。

## PsDscAllowPlainTextPassword

最初のエラー メッセージには、ドキュメントの URL があります。
このリンクでは、[ConfigurationData](https://msdn.microsoft.com/en-us/powershell/dsc/configdata) 構造と証明書を使用してパスワードを暗号化する方法を説明しています。
証明書と DSC の詳細については、[この投稿をご覧ください](http://aka.ms/certs4dsc)。

プレーンテキスト パスワードを強制的に使用するには、次のようにリソースの構成データ セクションに `PsDscAllowPlainTextPassword` キーワードが必要です。

```PowerShell
Configuration DomainCredentialExample
{
    param
    (
        [PSCredential] $DomainCredential
    )
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    node localhost
    {
        Group DomainUserToLocalGroup
        {
            GroupName        = 'ApplicationAdmins'
            MembersToInclude = 'contoso\alice'
            Credential       = $DomainCredential
        }
    }
}

$cd = @{
    AllNodes = @(
        @{
            NodeName = 'localhost'
            PSDscAllowPlainTextPassword = $true
        }
    )
}

$cred = Get-Credential -UserName contoso\genericuser -Message "Password please"
DomainCredentialExample -DomainCredential $cred -ConfigurationData $cd
```

*`NodeName` にアスタリスクは指定できません。特定のノード名が必須です。*

**Microsoft は、重大なセキュリティ リスクのためのプレーン テキスト パスワードを使用しないが表示されます。**

## ドメイン資格情報

構成スクリプト例を (暗号化して、またはしないで) もう一度実行しても、資格情報のドメイン アカウントを使用することは推奨されないという警告が生成されます。
ローカル アカウントを使用すると、他のサーバーで使用可能なドメイン資格情報が漏えいする可能性がなくなります。

**DSC リソースと資格情報を使用している場合は、ローカル アカウント可能な場合は、ドメイン アカウントよりも優先されます。**

ある場合、' \' または '@' で、 `Username` 、資格情報、DSC のプロパティを使用すると、ドメイン アカウントとして扱われます。
ユーザー名のドメイン部分には、"localhost"、"127.0.0.1"、および "::1" の例外があります。

## PSDscAllowDomainUser

上記の DSC `Group` リソースの例では、Active Directory ドメインのクエリを実行するにはドメイン アカウントが*必要です*。
この場合は、次のように `ConfigurationData` ブロックに `PSDscAllowDomainUser` プロパティを追加します。

```PowerShell
$cd = @{
    AllNodes = @(
        @{
            NodeName = 'localhost'
            PSDscAllowDomainUser = $true
            # PSDscAllowPlainTextPassword = $true
            CertificateFile = "C:\PublicKeys\server1.cer"
        }
    )
}
```

これで、構成スクリプトによってエラーや警告なしで MOF ファイルが生成されます。




<!--HONumber=Oct16_HO1-->


