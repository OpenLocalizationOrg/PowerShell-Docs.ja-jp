---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "デモ エンドポイントを作り直す"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: acd2cfbd038250a26236c875d0e8b03a32cd84f9

---

# デモ エンドポイントを作り直す
このセクションでは、前のセクションで使用したデモ エンドポイントの完全なレプリカを生成する方法について説明します。
ここでは、PowerShell セッション構成とロール機能を含む、JEA を理解するために必要な中心概念について説明します。

## PowerShell セッション構成
前のセクションで JEA を使用したとき、次のコマンドを実行することから始めました。

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEA_Demo -Credential $NonAdminCred
```

ほとんどのパラメーターは見ればわかりますが、*ConfigurationName* パラメーターは、最初はわかりにくいかもしれません。
そのパラメーターは、接続される PowerShell セッション構成を指定していました。

*PowerShell セッション構成*は、PowerShell エンドポイントの別名です。
それは、ユーザーが接続して PowerShell の機能にアクセスする比喩的な "場所" です。
セッション構成の設定方法に基づいて、接続しているユーザーに異なる機能を提供できます。
JEA では、セッション構成を使用して、PowerShell の機能を限定されたセットに制限し、特権のある仮想アカウントとして実行します。

既にいくつかの PowerShell セッション構成がコンピューターに登録されていますが、それぞれの設定はわずかに異なります。
ほとんどは Windows に付属していますが、"JEA_Demo" セッション構成は「[前提条件](prerequisites.md)」セクションで設定したものです。
Administrator PowerShell プロンプトで次のコマンドを実行することで、登録されているすべてのセッション構成を確認できます。

```PowerShell
Get-PSSessionConfiguration
```

## PowerShell セッション構成ファイル
新しいセッション構成は、新しい *PowerShell セッション構成ファイル*を登録することで作成できます。
セッション構成ファイルのファイル拡張子は ".pssc" です。
セッション構成ファイルは、New-PSSessionConfigurationFile コマンドレットを使用して生成できます。

この後、JEA 用の新しいセッション構成を作成して登録します。

## PowerShell セッション構成を生成して変更する
次のコマンドを実行して、PowerShell セッション構成の "スケルトン" ファイルを生成します。

```PowerShell
New-PSSessionConfigurationFile -Path "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

> **ヒント:** 既定では、スケルトン ファイルには、最も一般的な構成設定のみが含まれます。
> `-Full` パラメーターを使用して、適用できるすべての設定を、生成される PSSC に含めてください。

PowerShell ISE または任意のテキスト エディターでファイルを開きます。

```PowerShell
ise "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

ファイルの次のフィールドを、その下に示されている値に更新します。(管理者以外のセキュリティ グループに置き換えるのを忘れないでください)。

```PowerShell
# OLD: SessionType = 'Default'
SessionType = 'RestrictedRemoteServer'

# OLD: TranscriptDirectory = 'C:\Transcripts\'
TranscriptDirectory = "C:\ProgramData\JEAConfiguration\Transcripts"

# OLD: # RunAsVirtualAccount = $true
RunAsVirtualAccount = $true

# OLD: RoleDefinitions = @{ 'CONTOSO\SqlAdmins' = @{ RoleCapabilities = 'SqlAdministration' }; 'CONTOSO\ServerMonitors' = @{ VisibleCmdlets = 'Get-Process' } }
RoleDefinitions = @{'CONTOSO\JEA_NonAdmin_Operator' = @{ RoleCapabilities =  'Maintenance' }}
```

各エントリの意味は次のとおりです。

1.  *SessionType* フィールドは、このエンドポイントで使用する事前設定された既定の設定を定義します。
*RestrictedRemoteServer* は、リモート管理を行うために必要な最小限の設定を定義します。
既定では、*RestrictedRemoteServer* エンドポイントは、Get-Command、Get-FormatData、Select-Object、Get-Help、Measure-Object、Exit-PSSession、Clear-Host、および Out-Default を公開します。
それは、*ExecutionPolicy* を *RemoteSigned* に設定し、*LanguageMode* を *NoLanguage* に設定します。
これらの設定の実際の効果は、エンドポイントを構成するためのセキュリティで保護された最小限の構成の開始点になることです。

2.  *RoleDefinitions* フィールドは、ロール機能を特定のグループに割り当てます。
これは、特権アカウントとして誰が何を実行できるかを定義します。
このフィールドを使用して、接続しているユーザーがグループ メンバーシップに基づいて利用できる機能を指定できます。
これは、JEA の RBAC 機能の核心です。
この例では、事前に作成された "保守管理用の" ロール機能を "Contoso\JEA_NonAdmin_Operator" グループのメンバーに公開しています。

3.  *RunAsVirtualAccount* フィールドは、このエンドポイントで PowerShell を仮想アカウント "として (RunAs)" 実行する必要があることを示します。
既定では、仮想アカウントは、組み込み Administrators グループのメンバーです。
ドメイン コントローラーでは、既定では、Domain Administrators グループのメンバーです。
このガイドの後半で、仮想アカウントの特権をカスタマイズする方法について説明します。

4.  *TranscriptDirectory* フィールドは、各リモート セッションの後で "肩越しの" PowerShell トランスクリプトを保存する場所を定義します。
これらのトランスクリプトを使用して、各セッションで実行された操作を読みやすい方法で調べることができます。
PowerShell トランスクリプトについて詳しくは、[こちらのブログ投稿](http://blogs.msdn.com/b/powershell/archive/2015/06/09/powershell-the-blue-team.aspx)をご覧ください。
注: 標準の Windows Eventing も、各ユーザーが PowerShell で何を実行したかについての情報をキャプチャします。
トランスクリプトのほうが、少し読みやすくなっています。

最後に、変更を *JEADemo2.pssc* に保存します。

## PowerShell セッション構成を適用する

セッション構成ファイルからエンドポイントを作成するには、ファイルを登録する必要があります。
これを行うには、次の情報が必要です。

1. セッション構成ファイルのパス。
2. 登録されるセッション構成の名前。 これは、ユーザーが `Enter-PSSession` または `New-PSSession` を使用してエンドポイントに接続するときに "ConfigurationName" パラメーターに指定する名前と同じです。

ローカル コンピューターにセッション構成を登録するには、次のコマンドを実行します。

```PowerShell
Register-PSSessionConfiguration -Name 'JEADemo2' -Path "$env:ProgramData\JEAConfiguration\JEADemo2.pssc"
```

以上で、 JEA エンドポイントが設定されました。

## エンドポイントをテストする
「[JEA の使用](using-jea.md)」セクションに示されている手順をもう一度実行して、新しいエンドポイントが意図したとおりに動作していることを確認します。
`Enter-PSSession` に構成名を指定するときに、新しいエンドポイント名 (JEADemo2) を必ず使用してください。

```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName JEADemo2 -Credential $NonAdminCred
```

## 主要概念
**PowerShell セッション構成**: *PowerShell エンドポイント* と呼ばれることもあります。これは、ユーザーが接続して PowerShell の機能にアクセスする比喩的な "場所" です。
`Get-PSSessionConfiguration` を実行することで、システムに登録されているセッション構成を一覧表示できます。
特別な方法で構成した PowerShell セッション構成を *JEA エンドポイント*と呼ぶことができます。

**PowerShell セッション構成ファイル (.pssc)**: 登録すると、PowerShell セッション構成の設定を定義するファイルです。
エンドポイントに接続できるユーザー ロール、仮想アカウント "として (RunAs)" 実行するアカウントなどの仕様が含まれます。     

**ロール定義**: セッション構成ファイル内のフィールドであり、接続しているユーザーに与えられるロール機能を定義します。
これは、特権アカウントとして、*誰が**何を*実行できるかを定義します。
これは、JEA の RBAC 機能の核心です。

**SessionType**: セッション構成の既定の設定を表すセッション構成ファイル内のフィールドです。
JEA エンドポイントでは、このフィールドは RestrictedRemoteServer に設定する必要があります。

**PowerShell トランスクリプト**: PowerShell セッションの "肩越しの" ビューを含むファイルです。
TranscriptDirectory フィールドを使用して、JEA セッションのトランスクリプトを生成するように PowerShell を設定できます。
トランスクリプトについて詳しくは、[こちらのブログ投稿](https://technet.microsoft.com/en-us/magazine/ff687007.aspx)をご覧ください。




<!--HONumber=Oct16_HO1-->


