---
description: 
manager: dongill
ms.topic: article
author: jpjofre
ms.prod: powershell
keywords: "PowerShell, コマンドレット, JEA"
ms.date: 2016-06-22
title: "エンド ツー エンド - Active Directory"
ms.technology: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: e7ea3957ce3bbd3ce0fc072a82cd108606f05614

---

# エンド ツー エンド - Active Directory
プログラムの適用範囲が拡大されたと思ってください。
Active Directory の操作を実行するために、ドメイン コントローラーに JEA を追加する必要があります。
ヘルプ デスクのスタッフは、JEA を使用して、アカウントのロック解除、パスワードのリセット、その他の類似する操作を実行することになります。

まったく新しいコマンド セットを、異なるユーザー グループに公開する必要があります。
それに加えて、公開する必要がある既存の Active Directory スクリプトが多数あります。
このセクションでは、このタスクを実行するためのセッション構成とロール機能の作成について説明します。

## 前提条件
このセクションの手順を実行するには、ドメイン コントローラーを操作する必要があります。
ドメイン コントローラーにアクセスしたことがなくても大丈夫です。
熟知している他のシナリオやロールでの作業を想像しながら、読み進めてください。
新しいドメイン コントローラーをすぐに設定する場合は、付録の「[ドメイン コントローラーの作成](.\creating-a-domain-controller.md)」を参照してください。

## 新しいロール機能とセッション構成の作成手順

新しいロール機能の作成は、最初はやっかいな作業に思えるかもしれませんが、それは、次に示す非常に単純な手順に分解することができます。

1.  有効にする必要があるタスクを特定する
2.  必要に応じて、それらのタスクを制限する
3.  タスクが JEA で機能することを確認する
4.  タスクをロール機能ファイルに配置する
5.  ロール機能を公開するセッション構成を登録する

## 手順 1: 何を公開する必要があるかを識別する
新しいロール機能またはセッション構成を作成する前に、ユーザーが JEA エンドポイントで何を実行する必要があり、それらを PowerShell でどのように実行する必要があるかを、すべて識別する必要があります。
これには、相当量の要件の収集と調査が伴います。
このプロセスをどのように進めるかは、組織と目標によって決まります。
重要なのは、要件の収集と調査を、非常に意味のある作業として実施することです。
これは、JEA の採用プロセスの中で、最も困難な手順である可能性があります。

### リソースを見つける
Active Directory 管理エンドポイントを作成するための調査で利用されている可能性があるオンライン リソースがあります。
-   [Active Directory PowerShell の概要](http://blogs.msdn.com/b/adpowershell/archive/2009/03/05/active-directory-powershell-overview.aspx)
-   [Active Directory 用の PowerShell ガイドには、「cmd」](http://blogs.technet.com/b/ashleymcglone/archive/2013/01/02/free-download-cmd-to-powershell-guide-for-ad.aspx)

### リストを作成する
このセクションの残りの部分で扱う 10 個の操作を次に示します。
これは単なる例であり、組織の要件によって異なる可能性があることに注意してください。

|操作                                                         |PowerShell コマンド                                             |
|---------------------------------------------------------------|---------------------------------------------------------------|
|アカウントのロック解除                                                 |`Unlock-ADAccount`                                             |
|パスワードのリセット                                                 |`Set-ADAccountPassword`、`Set-ADUser -ChangePasswordAtLogon`|
|ユーザーのタイトルの変更                                          |`Set-ADUser -Title`                                            |  
|ロックアウト済み、無効化、非アクティブなどの状態になっている AD アカウントの検索 |`Search-ADAccount`                                             |
|グループへのユーザーの追加                                              |`Add-ADGroupMember -Identity (with whitelist) -Members`        |
|グループからのユーザーの削除                                         |`Remove-ADGroupMember -Identity (with whitelist) -Members`     |
|ユーザー アカウントの有効化                                          |`Enable-ADAccount`                                             |
|ユーザー アカウントの無効化                                         |`Disable-ADAccount`                                            |

## 手順 2: 必要に応じてタスクを制限する

操作リストができたら、各コマンドの機能を徹底的に検討する必要があります。
これを行う 2 つの重要な理由があります。

1.  意図以上の機能がユーザーに与えられる可能性があります。
たとえば、`Set-ADUser` は非常に強力で柔軟なコマンドです。
このコマンドで実行できるすべての操作をヘルプ デスクのユーザーに公開する必要はありません。  

2.  さらに悪いのは、ユーザーが JEA の制限を逃れることを可能にするコマンドが公開される可能性があることです。
これが発生した場合、JEA はセキュリティ境界として機能しなくなります。
コマンドは慎重に選択してください。
たとえば、Invoke-Expression は、ユーザーがコードを無制限に実行することを可能にします。
このトピックの詳細については、「コマンドを制限する際の考慮事項」セクションを参照してください。

各コマンドを検討した後、次のように制限することを決定したとします。

1.  `Set-ADUser` タイトルには、パラメーターを使用して実行を許可するのみ

2.  `Add-ADGroupMember`  `Remove-ADGroupMember` の特定のグループでのみ動作する必要があります

### 手順 3: タスクが JEA で動作することを確認する
これらのコマンドレットは、制限された JEA 環境では単純に使用できない場合があります。
JEA は *NoLanguage* モードで実行されますが、このモードは、なによりもユーザーが変数を使用することを禁止します。
スムーズなエンド ユーザー エクスペリエンスを実現するには、いくつかの事項を確認する必要があります。

例として、`Set-ADAccountPassword` を検討します。
-NewPassword パラメーターには、セキュリティで保護された文字列が必要です。
多くの場合、ユーザーは、セキュリティで保護された文字列を作成し、それを変数として渡します (下記参照)。

```PowerShell
$newPassword = Read-Host -Prompt "Specify a new password" -AsSecureString
Set-ADAccountPassword -Identity mollyd -NewPassword $newPassword -Reset
```

ただし、*NoLanguage* モードでは、変数は使用できません。
この制限は、次の 2 つの方法で回避できます。

1.  変数を割り当てずにコマンドを実行することをユーザーに要求する。
これは簡単に構成できますが、エンドポイントを使用するオペレーターにとってはわずらわしい操作になる可能性があります。
パスワードをリセットするたびに、この文字列を入力したいと思う人がいるでしょうか。
```PowerShell
Set-ADAccountPassword -Identity mollyd -NewPassword (Read-Host -Prompt "Specify a new password" -AsSecureString) -Reset
```

2.  エンド ユーザーに公開されるスクリプトまたは関数で複雑さをラップする。
公開するスクリプトと関数が実行されるコンテキストに制限はありません。変数の使用と他のコマンドの呼び出しがある関数を問題なく記述できます。
この方法は、エンド ユーザー エクスペリエンスを単純化し、エラーを防止し、PowerShell の知識が少なくても実行でき、余分な機能を意図せずに公開する可能性を低下させます。
唯一の欠点は、関数を作成して管理するコストが発生することです。

### 追加情報: モジュールへの関数の追加
方法 2 を採用する場合は、`Reset-ContosoUserPassword` という名前の PowerShell 関数を記述します。
この関数は、ユーザーのパスワードをリセットするときに実行する必要があるすべての操作を実行します。
組織によっては、手の込んだ複雑な操作が必要な場合があります。
これは単なる例であるため、ここで説明するコマンドは、パスワードをリセットし、ログオン時にパスワードを変更することをユーザーに要求するだけです。
これを、最後のセクションで作成した Contoso_AD_Module モジュールに配置します。

1. PowerShell ISE で Contoso_AD_Module.psm1 を開きます。
```PowerShell
ise 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\Contoso_AD_Module.psm1'
```

2. Crtl + J キーを押して、スニペット メニューを開きます。

3. "function" が見つかるまでキーを押し続けて、Enter キーを押します。
これにより、関数の極めて基本的なスケルトンが格納されます。

4. 関数の名前を「Reset-ContosoUserPassword」に変更します。  

5. いずれかのパラメーターの名前を「Identity」にし、2 つ目を削除します。

6. 以下を関数の本文にコピーします。
```PowerShell
# Get the new password
$NewPassword = Read-Host -Prompt "Enter a new password" -AsSecureString
# Reset the password
Set-ADAccountPassword -Identity $Identity -NewPassword $NewPassword -Reset
# Require the user to reset at next logon
Set-ADUser -Identity $Identity -ChangePasswordAtLogon
```

7. ファイルを保存します。
最終的に、次のようなものになります。
```PowerShell
function Reset-ContosoUserPassword ($Identity)
{
# Get the new password
$NewPassword = Read-Host -Prompt "Enter a new password" -AsSecureString
# Reset the password
Set-ADAccountPassword -Identity $Identity -NewPassword $NewPassword -Reset
# Require the user to reset at next logon
Set-ADUser -Identity $Identity -ChangePasswordAtLogon
}
```
これで、ユーザーは `Reset-ContosoUserPassword` を簡単に呼び出すことができ、安全なインライン文字列を作成する構文を覚えて置く必要もなくなりました。

## 手順 4: ロール機能ファイルを編集する
「[ロール機能の作成](./role-capabilities.md#role-capability-creation)」セクションで、空のロール機能ファイルを作成しました。
このセクションでは、このファイルに値を設定します。

PowerShell ISE でロール機能ファイルを開いて開始します。
```PowerShell
ise 'C:\Program Files\WindowsPowerShell\Modules\Contoso_AD_Module\RoleCapabilities\ADHelpDesk.psrc'
```
ファイルを以下のように変更します。
```PowerShell
# OLD: VisibleCmdlets = 'Invoke-Cmdlet1', @{ Name = 'Invoke-Cmdlet2'; Parameters = @{ Name = 'Parameter1'; ValidateSet = 'Item1', 'Item2' }, @{ Name = 'Parameter2'; ValidatePattern = 'L*' } }
VisibleCmdlets =
    'Unlock-ADAccount',
    'Search-ADAccount',
    'Enable-ADAccount',
    'Disable-ADAccount',
    @{ Name = 'Set-ADUser'; Parameters = @{ Name = 'Title'; ValidateSet = 'Manager', 'Engineer' }},
    @{ Name = 'Add-ADGroupMember'; Parameters =
        @{Name = 'Identity'; ValidateSet = 'TestGroup'},
        @{Name = 'Members'}},
    @{ Name = 'Remove-ADGroupMember'; Parameters =
        @{Name = 'Identity'; ValidateSet = 'TestGroup'},
        @{Name = 'Members'}}

# OLD: VisibleFunctions = 'Invoke-Function1', @{ Name = 'Invoke-Function2'; Parameters = @{ Name = 'Parameter1'; ValidateSet = 'Item1', 'Item2' }, @{ Name = 'Parameter2'; ValidatePattern = 'L*' } }
VisibleFunctions = 'Reset-ContosoUserPassword'
```

ここで、いくつかの注意点があります。
1.  PowerShell は、ロール機能に必要なモジュールの自動読み込みを試行します。
モジュールが自動的に読み込まれない問題が発生した場合、ModulesToImport フィールドにモジュール名を明示的にリストする必要がある場合があります。

2.  コマンドがコマンドレットまたは関数のいずれであるかわからない場合は、`Get-Command` を実行して、"CommandType" プロパティを確認します。

3.  一連の使用可能値を簡単に定義できない場合は、ValidatePattern により、正規表現を使って、パラメーター引数を制限できます。
1 つのパラメーターについて ValidatePattern と ValidateSet の両方を定義することはできません。

## 手順 5: 新しいセッション構成を登録する
次に、ロール機能を JEA_NonAdmin_HelpDesk グループのメンバーに公開する新しいセッション構成ファイルを作成します。

まず、PowerShell ISE に新しい空のセッション構成ファイルを作成して、開きます。
```PowerShell
New-PSSessionConfigurationFile -Path "$env:ProgramData\JEAConfiguration\HelpDeskDemo.pssc"
ise "$env:ProgramData\JEAConfiguration\HelpDeskDemo.pssc"
```
PSSC ファイル内の次のフィールドを変更します。
独自の環境で作業している場合は、CONTOSO\JEA_NonAdmins_Helpdesk を自身の管理者以外のユーザーまたはグループに置き換える必要があります。
```PowerShell
# OLD: Description = ''
Description = 'An endpoint for Active Directory tasks.'

# OLD: SessionType = 'Default'
SessionType = 'RestrictedRemoteServer'

# OLD: TranscriptDirectory = 'C:\Transcripts\'
TranscriptDirectory = "C:\ProgramData\JEAConfiguration\Transcripts"

# OLD: RunAsVirtualAccount = $true
RunAsVirtualAccount = $true

# OLD: RoleDefinitions = @{ 'CONTOSO\SqlAdmins' = @{ RoleCapabilities = 'SqlAdministration' }; 'CONTOSO\ServerMonitors' = @{ VisibleCmdlets = 'Get-Process' } }
RoleDefinitions = @{ 'CONTOSO\JEA_NonAdmin_HelpDesk' = @{ RoleCapabilities =  'ADHelpDesk' }}
```
セッション構成の保存と登録
```PowerShell
Register-PSSessionConfiguration -Name ADHelpDesk -Path "$env:ProgramData\JEAConfiguration\HelpDeskDemo.pssc"
```
## テストしてみましょう。
管理者以外のユーザー資格情報を取得します。
```PowerShell
$HelpDeskCred = Get-Credential
```
「[ユーザーとグループの設定](creating-a-domain-controller.md#set-up-users-and-groups)」に従っている場合、資格情報は次のようになります。
-   ユーザー名 = HelpDeskUser
-   パスワード = "pa$$w0rd"

管理者以外の資格情報を使用して AD ヘルプデスクのエンドポイントにリモート接続します。
```PowerShell
Enter-PSSession -ComputerName . -ConfigurationName ADHelpDesk -Credential $HelpDeskCred
```
Set-ADUser を使って、ユーザーの役職をリセットします。
```PowerShell
Set-ADUser -Identity OperatorUser -Title Engineer
```
役職が変更されていることを確認します。
```PowerShell
Get-ADUser -Identity OperatorUser -Property Title
```
ここで、Add-ADGroupMember を使って、ユーザーを TestGroup に追加します。
注: TestGroup は事前に作成しておいてください。
```PowerShell
Add-ADGroupMember TestGroup -Member OperatorUser -Verbose
```
セッションを終了します。
```PowerShell
Exit-PSSession
```
## 主要概念
**NoLanguage モード**: PowerShell が NoLanguage モードの場合は、ユーザーが実行できるのはコマンドのみです。言語要素は使用できません。
詳細を確認するには、`Get-Help about_Language_Modes` を実行してください。

**PowerShell 関数**: PowerShell 関数は、名前で呼び出すことのできる PowerShell コードのビットです。
詳細を確認するには、`Get-Help about_Functions` を実行してください。

**ValidateSet/ValidatePattern**: コマンドの公開時に、特定のパラメーターに有効な引数を制限できます。
ValidateSet は、有効な引数の特定のリストです。
ValidatePattern は、そのパラメーターの引数が一致する必要のある正規表現です。




<!--HONumber=Oct16_HO1-->


