---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "ロール機能"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: a3dd4a217f5b1fd80e97adf802c65073ca015bbc

---

# ロール機能

## 概要
前のセクションで、"RoleDefinitions" フィールドは、どのグループがどのロール機能にアクセスできるかを定義することを説明しました。
"ロール機能とは何か" と思うかもしれません。
このセクションでは、その疑問に答えます。  

## PowerShell ロール機能の概要
PowerShell ロール機能は、ユーザーが JEA エンドポイントで "何を" 実行できるかを定義します。
それらは、表示されるコマンドやアプリケーションなどを示す詳細なホワイトリストです。
ロール機能は、拡張子が ".psrc" のファイルによって定義されます。

## ロール機能の内容
前に使用したデモ用のロール機能ファイルを調べて変更することから始めます。
環境全体にセッション構成を展開しているが、ユーザーに公開される機能の変更を求めるフィードバックを受けていると想像してください。
マシンを再起動する機能を要求しているオペレーターがいます。彼らは、ネットワーク設定に関する情報も取得できることを望んでいます。
セキュリティ チームから、制限なしでユーザーに "Restart-Service" の実行を許可することは容認できないという指示を受けています。
オペレーターが再起動できるサービスを制限する必要があります。

これらの変更を行うには、管理者として PowerShell ISE を実行し、次のファイルを開くことから始めます。

```
C:\Program Files\WindowsPowerShell\Modules\Demo_Module\RoleCapabilities\Maintenance.psrc
```

次に、ファイルの次の行を検索して更新します。

```PowerShell
# OLD: VisibleCmdlets = 'Restart-Service'
VisibleCmdlets = 'Restart-Computer',
                 @{
                     Name = 'Restart-Service'
                     Parameters = @{ Name = 'Name'; ValidateSet = 'Spooler' }
                 },
                 'NetTCPIP\Get-*'

# OLD: VisibleExternalCommands = 'Item1', 'Item2'
VisibleExternalCommands = 'C:\Windows\system32\ipconfig.exe'
```

ここには、興味深い例がいくつか含まれています。

1.  オペレーターは -Name パラメーターを指定した場合のみ Restart-Service を使用でき、そのパラメーターの引数として "Spooler" だけを指定できるように、Restart-Service に制限を設定します。
必要に応じて、"ValidateSet" ではなく "ValidatePattern" を使用する正規表現を使用して、引数を制限することもできます。

2.  NetTCPIP モジュールの "Get"動詞を使用するすべてのコマンドを公開しています。
"Get" コマンドは、通常はシステム状態を変更しないため、これらは比較的安全な操作です。
とはいえ、JEA を介して公開するすべてのコマンドを注意深く調べることを強く推奨します。

3.  VisibleExternalCommands を使用する実行可能ファイル (ipconfig) を公開しています。
このフィールドを使用して、すべての PowerShell スクリプトを公開することもできます。
重要なのは、似たような名前の (不正の可能性がある) プログラムがユーザーのパスに配置されて代わりに実行されることがないように、外部コマンドの完全パスを常に指定することです。

ファイルを保存した後、変更が機能することを確認するために、デモ エンドポイントにもう一度接続します。

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEADemo2 -Credential $NonAdminCred
Get-Command
```
ロール機能ファイルのみを更新したため、セッション構成を再登録する必要はありません。
PowerShell は、ユーザーが接続するときに、更新されたロール機能を自動的に検索します。
ロール機能は、セッションの開始時に読み込まれるため、既存のセッションはロール機能ファイルの更新に影響されません。

次に、Restart-Computer を -WhatIf パラメーターを指定して実行することで、コンピューターを再起動できることを確認します (コンピューターを実際に再起動するのでない限り、このパラメーターを指定します)。

```PowerShell
Restart-Computer -WhatIf
```

"Ipconfig" を実行できることを確認します。

```PowerShell
ipconfig
```

最後に、Restart-Service は Spooler サービスに対してのみ機能することを確認します。

```PowerShell
Restart-Service Spooler # This should work
Restart-Service WSearch # This should fail
```

操作を終了したら、セッションを終了します。

```PowerShell
Exit-PSSession
```

## ロール機能の作成
次のセクションで、AD ヘルプ デスク ユーザー用の JEA エンドポイントを作成します。
準備として、そのセクションで値を入力するための空白のロール機能ファイルを作成します。
ロール機能は、セッションの開始時に解決されるように、有効な PowerShell モジュールの "RoleCapabilities" フォルダー内に作成する必要があります。

PowerShell モジュールは、本質的には PowerShell 機能のパッケージです。
PowerShell 関数、コマンドレット、DSC リソース、ロール機能などを含めることができます。
PowerShell コンソールで `Get-Help about_Modules` を実行することで、モジュールに関する情報を見つけることができます。

空白のロール機能ファイルがある新しい PowerShell モジュールを作成するには、次のコマンドを実行します。  

```PowerShell
# Create a new folder for the module.
New-Item -Path 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module' -ItemType Directory

# Add a module manifest to contain metadata for this module.
New-ModuleManifest -Path 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\Contoso_AD_Module.psd1' -RootModule Contoso_AD_Module.psm1

# Create a blank script module. You'll use this for custom functions in the next section.
New-Item -Path 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\Contoso_AD_Module.psm1' -ItemType File

# Create a RoleCapabilities folder in the Contoso_AD_Module folder. PowerShell expects Role Capabilities to be located in a "RoleCapabilities" folder within a module.
New-Item -Path 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\RoleCapabilities' -ItemType Directory

# Create a blank Role Capability in your RoleCapabilities folder. Running this command without any additional parameters just creates a blank template.
New-PSRoleCapabilityFile -Path 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\RoleCapabilities\ADHelpDesk.psrc'
```

以上で、空白のロール機能ファイルが作成されました。
これは、次のセクションで使用します。

## 主要概念
**ロール機能 (.psrc)**: ユーザーが JEA エンドポイントで "何を" 実行できるかを定義するファイルです。
それは、表示されるコマンドやコンソール アプリケーションなどを示す詳細なホワイトリストです。
PowerShell でロール機能を検出するには、有効な PowerShell モジュールの "RoleCapabilities" フォルダーにそれらを配置する必要があります。

**PowerShell モジュール**: PowerShell 機能のパッケージです。
PowerShell 関数、コマンドレット、DSC リソース、ロール機能などを含めることができます。
PowerShell モジュールが自動的に読み込まれるようにするには、`$env:PSModulePath` のパスの下に配置する必要があります。




<!--HONumber=Oct16_HO1-->


