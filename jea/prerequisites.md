---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "前提条件"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: ac9231a475ba84e9051bbd06a65f3f20c9e49846

---

# 前提条件

## 初期状態
このセクションの操作を開始する前に、次の状態になっていることを確認してください。

1. システムで JEA が利用可能である。 現在サポートされているオペレーティング システムと必要なダウンロードを[こちら](./README.md)で確認してください。
2. JEA を試そうとしているコンピューターの管理者権限を持っていること。
3. コンピューターがドメインに参加していること。
ドメインがない場合に新しいドメインをサーバー上にすぐにセットアップするには、「[ドメイン コントローラーの作成](#creating-a-domain-controller)」セクションを参照してください。

## PowerShell リモート処理を有効にする
JEA による管理は、PowerShell リモート処理を通して行われます。
Administrator PowerShell ウィンドウで次のコマンドを実行して、PowerShell リモート処理を有効にし、適切に構成されるようにします。

```PowerShell
Enable-PSRemoting
```

PowerShell リモート処理に慣れていない場合は、`Get-Help about_Remote` を実行して、重要な基本概念について理解しておくことをお勧めします。

## ユーザーまたはグループを識別する
JEA の動作を示すために、このガイド全体で使用する管理者以外のユーザーとグループを識別する必要があります。

既存のドメインを使用している場合は、特権のないユーザーとグループを識別するか、そのようなユーザーとグループをいくつか作成してください。
これらの管理者以外のユーザーで、JEA にアクセスします。
スクリプトの一番上に `$NonAdministrator` 変数がある場合は、その変数に選択した管理者以外のユーザーまたはグループを常に割り当ててください。

新しいドメインを最初から作成する場合は、はるかに簡単です。
付録の「[ユーザーとグループの設定](creating-a-domain-controller.md#set-up-users-and-groups)」セクションに従って、管理者以外のユーザーとグループを作成してください。
そのセクションに従って作成したグループの既定値は `$NonAdministrator` になります。

## メンテナンス ロール機能ファイルの設定
PowerShell で次のコマンドを実行して、次のセクションで使用するデモ用のロール機能ファイルを作成します。
このファイルが何をするかについては、このガイドの後半で説明します。

```PowerShell
# Fields in the role capability
$MaintenanceRoleCapabilityCreationParams = @{
    Author = 'Contoso Admin'
    CompanyName = 'Contoso'
    VisibleCmdlets = 'Restart-Service'
    FunctionDefinitions =
            @{ Name = 'Get-UserInfo'; ScriptBlock = { $PSSenderInfo } }
}

# Create the demo module, which will contain the maintenance Role Capability File
New-Item -Path "$env:ProgramFiles\WindowsPowerShell\Modules\Demo_Module" -ItemType Directory
New-ModuleManifest -Path "$env:ProgramFiles\WindowsPowerShell\Modules\Demo_Module\Demo_Module.psd1"
New-Item -Path "$env:ProgramFiles\WindowsPowerShell\Modules\Demo_Module\RoleCapabilities" -ItemType Directory

# Create the Role Capability file
New-PSRoleCapabilityFile -Path "$env:ProgramFiles\WindowsPowerShell\Modules\Demo_Module\RoleCapabilities\Maintenance.psrc" @MaintenanceRoleCapabilityCreationParams
```

## デモ用のセッション構成ファイルを作成して登録する
次のコマンドを実行して、次のセクションで使用するデモ用のセッション構成ファイルを作成します。
このファイルが何をするかについては、このガイドの後半で説明します。

```PowerShell
# Determine domain
$domain = (Get-CimInstance -ClassName Win32_ComputerSystem).Domain

# Replace with your non-admin group name
$NonAdministrator = "$domain\JEA_NonAdmin_Operator"

# Specify the settings for this JEA endpoint
# Note: You will not be able to use a virtual account if you are using WMF 5.0 on Windows 7 or Windows Server 2008 R2
$JEAConfigParams = @{
    SessionType = 'RestrictedRemoteServer'
    RunAsVirtualAccount = $true
    RoleDefinitions = @{
        $NonAdministrator = @{ RoleCapabilities = 'Maintenance' }
    }
    TranscriptDirectory = "$env:ProgramData\JEAConfiguration\Transcripts"
}

# Set up a folder for the Session Configuration files
if (-not (Test-Path "$env:ProgramData\JEAConfiguration"))
{
    New-Item -Path "$env:ProgramData\JEAConfiguration" -ItemType Directory
}

# Specify the name of the JEA endpoint
$sessionName = 'JEA_Demo'

if (Get-PSSessionConfiguration -Name $sessionName -ErrorAction SilentlyContinue)
{
    Unregister-PSSessionConfiguration -Name $sessionName -ErrorAction Stop
}

New-PSSessionConfigurationFile -Path "$env:ProgramData\JEAConfiguration\JEADemo.pssc" @JEAConfigParams

# Register the session configuration
Register-PSSessionConfiguration -Name $sessionName -Path "$env:ProgramData\JEAConfiguration\JEADemo.pssc"
```

## PowerShell モジュールのログを有効にする (省略可能)
次の手順で、システム上のすべての PowerShell 操作のログを有効にします。
これは JEA を動作させるために有効にする必要はありませんが、「[JEA のレポート](reporting-on-jea.md)」セクションで役に立ちます。

1. ローカル グループ ポリシー エディターを開きます。
2. "Computer Configuration\Administrative Templates\Windows Components\Windows PowerShell" に移動します。
3. [モジュール ログを有効にする] をダブルクリックします。
4. [有効] をクリックします。
5. [オプション] セクションで、モジュール名の横にある [表示] をクリックします。
6. 型"\ *"で、ポップアップ ウィンドウです。 これは、PowerShell がすべてのモジュールのコマンドを記録することを意味します。
7. [OK] をクリックしてポリシーを適用します。

注: グループ ポリシーを通してシステム全体の PowerShell トランスクリプトを有効にすることもできます。

**これでデモ エンドポイントと、コンピューターが構成されましたして JEA を使用する準備が整いましたされました。**




<!--HONumber=Oct16_HO1-->


