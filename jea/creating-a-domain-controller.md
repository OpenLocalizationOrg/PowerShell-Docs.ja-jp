---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "ドメイン コントローラーの作成"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 8473eb668e4da5bab01c2f2b7647cbced413bd22

---

### ドメイン コントローラーの作成

このドキュメントでは、お使いのコンピューターがドメインに参加していることを前提としています。
現在ドメインに参加していない場合は、このセクションを利用して、DSC を使ってドメイン コントローラーを簡単に立ち上げることができます。

#### 前提条件

1.  コンピューターが内部ネットワークにあること
2.  コンピューターが、既存のドメインに参加していないこと
3.  コンピューターが Windows Server 2016 を実行しているか、WMF 5.0 がインストールされていること

#### xActiveDirectory のインストール
コンピューターにアクティブなインターネット接続がある場合は、管理者特権の PowerShell ウィンドウで次のコマンドを実行します。
```PowerShell
Install-Module xActiveDirectory -Force
```
インターネット接続がない場合は、xActiveDirectory を別のコンピューターにインストールしてから、xActiveDirectory フォルダーをコンピューターの C:\Program Files\WindowsPowerShell\Modules フォルダーにコピーします。

インストールに成功したか確認するには、次のコマンドを実行します。
```PowerShell
Get-Module xActiveDirectory -ListAvailable
```

#### DSC を使用したドメインのセットアップ
PowerShell で次のスクリプトをコピーして、お使いのコンピューターを新しいドメインのドメイン コントローラーにします。
**執筆者のメモ: は使用されていない提供された資格情報と既知の問題です。安全のため、ローカルの管理者のパスワードを忘れないでください。**

```PowerShell
Set-Item WSMan:\localhost\Client\TrustedHosts -Value $env:COMPUTERNAME -Force

# This "MetaConfiguration" sets the DSC Engine to automatically reboot if required
[DscLocalConfigurationManager()]
Configuration MetaConfiguration
{
    Node $env:Computername
    {
        Settings
        {
            RebootNodeIfNeeded = $true
        }
    }

}

MetaConfiguration
# Apply the MetaConfiguration
Set-DscLocalConfigurationManager .\MetaConfiguration

# Configure a domain controller of a new "Contoso" domain
configuration DomainController
{
    param
    (
        $node,
        $cred
    )
    Import-DscResource -ModuleName xActiveDirectory

    Node $node
    {
        WindowsFeature ADDS
        {
            Ensure = 'Present'
            Name = 'AD-Domain-Services'
        }

        xADDomain Contoso
        {
            DomainName = 'contoso.com'
            DomainAdministratorCredential = $cred
            SafemodeAdministratorPassword = $cred
            DependsOn = '[WindowsFeature]ADDS'
        }

        file temp
        {
            DestinationPath = 'C:\temp.txt'
            Contents = 'Domain has been created'
            DependsOn = '[xADDomain]Contoso'
        }
    }
}

$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = $env:Computername
            PSDscAllowPlainTextPassword = $true
        }
    )
}

# Enter your desired password for the domain administrator (note, this will be stored as plain text)
DomainController -cred (Get-Credential -Message "Enter desired credential for domain administrator") -node $env:Computername -configurationData $ConfigData

# Apply the configuration to create the domain controller
Start-DSCConfiguration -path .\DomainController -ComputerName $env:Computername -Wait -Force -Verbose
```
コンピューターが数回再起動します。
ドメインが作成されたことを伝える C:\temp.txt というファイルが表示されたら、プロセスは完了しています。

#### ユーザーとグループの設定
次のコマンドは、ドメインにオペレーターとヘルプデスクのグループ、またそのグループのメンバーである対応する管理者以外のユーザーを設定します。
```PowerShell
# Make Groups
$NonAdminOperatorGroup = New-ADGroup -Name "JEA_NonAdmin_Operator" -GroupScope DomainLocal -PassThru
$NonAdminHelpDeskGroup = New-ADGroup -Name "JEA_NonAdmin_HelpDesk" -GroupScope DomainLocal -PassThru
$TestGroup = New-ADGroup -Name "Test_Group" -GroupScope DomainLocal -PassThru

# Make Users
$OperatorUser = New-ADUser -Name "OperatorUser" -AccountPassword (ConvertTo-SecureString 'pa$$w0rd' -AsPlainText -Force) -PassThru
Enable-ADAccount -Identity $OperatorUser

$HelpDeskUser = New-ADUser -name "HelpDeskUser" -AccountPassword (ConvertTo-SecureString 'pa$$w0rd' -AsPlainText -Force) -PassThru
Enable-ADAccount -Identity $HelpDeskUser

# Add Users to Groups
Add-ADGroupMember -Identity $NonAdminOperatorGroup -Members $OperatorUser
Add-ADGroupMember -Identity $NonAdminHelpDeskGroup -Members $HelpDeskUser
```




<!--HONumber=Oct16_HO1-->


