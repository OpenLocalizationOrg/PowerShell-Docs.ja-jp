---
title: "PowerShell.exe コマンドラインのヘルプ"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 1ab7b93b-6785-42c6-a1c9-35ff686a958f
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: c3b263110a908c28569cf3048a94d48da8316684

---

# PowerShell.exe コマンドラインのヘルプ
Windows PowerShell セッションを開始します。 PowerShell.exe を使用して、Cmd.exe などの別のツールのコマンド ラインから Windows PowerShell セッションを起動したり、PowerShell.exe を Windows PowerShell コマンド ラインで使用して、新しいセッションを開始したりすることができます。 パラメーターを使用して、セッションをカスタマイズします。

## 構文

```
PowerShell[.exe]
       [-EncodedCommand <Base64EncodedCommand>]
       [-ExecutionPolicy <ExecutionPolicy>]
       [-InputFormat {Text | XML}] 
       [-Mta]
       [-NoExit]
       [-NoLogo]
       [-NonInteractive] 
       [-NoProfile] 
       [-OutputFormat {Text | XML}] 
       [-PSConsoleFile <FilePath> | -Version <Windows PowerShell version>]
       [-Sta]
       [-WindowStyle <style>]
       [-File <FilePath> [<Args>]]
       [-Command { - | <script-block> [-args <arg-array>]
                     | <string> [<CommandParameters>] } ]

PowerShell[.exe] -Help | -? | /?
```

## パラメーター

### -EncodedCommand <Base64EncodedCommand>
Base 64 エンコード文字列版のコマンドを許可します。 このパラメーターは、複雑な引用符や中かっこを必要とするコマンドを Windows PowerShell に渡す場合に使用します。

### 実行ポリシー <ExecutionPolicy>
現在のセッションの既定の実行ポリシーを設定し、$env:PSExecutionPolicyPreference 環境変数に保存します。 このパラメーターを指定しても、レジストリで設定された Windows PowerShell の実行ポリシーが変更されるわけではありません。 使用できる値の一覧など、Windows PowerShell 実行ポリシーの詳細については、「about_Execution_Policies (http://go.microsoft.com/fwlink/?LinkID=135170)」をご覧ください。

### -File <FilePath> \[<Parameters>]
スクリプトで作成される関数と変数を現在のセッションで使用できるように、指定したスクリプトをローカル スコープ ("ドット ソース形式") で実行します。 スクリプト ファイルのパスと (存在する場合) パラメーターを入力します。 **File** はコマンド内の最後のパラメーターにする必要があります。これは、**File** パラメーター名の後に入力されるすべての文字が、スクリプト ファイルのパス、およびその後に続くスクリプト パラメーターとその値として解釈されるためです。

**File** パラメーターの値には、スクリプトのパラメーターとパラメーター値を含めることができます。 たとえば次のようになります。`-File .\Get-Script.ps1 -Domain Central`

通常、スクリプトのスイッチ パラメーターは、含めるか省略するかのいずれかです。 たとえば、次のコマンドを使用して、 **すべて** Get Script.ps1 スクリプト ファイルのパラメーター。 `-File .\Get-Script.ps1 -All`

まれに、スイッチ パラメーターにブール値を指定しなければならない場合があります。 **File** パラメーターの値にスイッチ パラメーターのブール値を指定するには、次のように、中かっこでパラメーター名と値を囲みます。`-File .\Get-Script.ps1 {-All:$False}`

### -InputFormat {Text | XML}
Windows PowerShell に送信されるデータ形式を記述します。 使用できる値は、"Text" (テキスト文字列) と "XML" (シリアル化された CLIXML 形式) です。

### -Mta
マルチスレッド アパートメントを使用して、Windows PowerShell を起動します。 このパラメーターは、Windows PowerShell 3.0 で導入されました。 Windows PowerShell 3.0 では、シングルスレッド アパートメント (STA) が既定値です。 Windows PowerShell 2.0 では、マルチスレッド アパートメント (MTA) が既定値です。

### -NoExit
スタートアップ コマンドを実行後、終了しません。

### -NoLogo
スタートアップ時に著作権の見出しを非表示にします。

### -NonInteractive
ユーザーに対話的なプロンプトを表示しません。

### -NoProfile
Windows PowerShell プロファイルを読み込みません。

### -OutputFormat {Text | XML}
Windows PowerShell からの出力の形式を決定します。 使用できる値は、"Text" (テキスト文字列) と "XML" (シリアル化された CLIXML 形式) です。

### -Psconsolefile <FilePath>
指定された Windows PowerShell コンソール ファイルを読み込みます。 コンソール ファイルの名前とパスを入力します。 コンソール ファイルを作成するには、Windows PowerShell で [Export-Console](https://technet.microsoft.com/en-us/library/4bab1c02-9e61-4aaf-9957-11d1934ef4ef) コマンドレットを使用します。

### -Sta
シングルスレッド アパートメントを使用して、Windows PowerShell を起動します。 Windows PowerShell 3.0 では、シングルスレッド アパートメント (STA) が既定値です。 Windows PowerShell 2.0 では、マルチスレッド アパートメント (MTA) が既定値です。

### バージョン <Windows PowerShell Version>
指定したバージョンの Windows PowerShell を起動します。 指定するバージョンがシステムにインストールされている必要があります。 Windows PowerShell 3.0 がコンピューターにインストールされている場合、使用できる値は "2.0" と "3.0" です。 既定値は "3.0" です。

Windows PowerShell 3.0 がコンピューターにインストールされていない場合、使用できる値は "2.0" のみです。 他の値は無視されます。

詳細については、「[Windows PowerShell ファースト ステップ ガイド [OLD MSDN]](https://technet.microsoft.com/en-us/library/69555d95-b481-43e1-86e7-b46d68b3e2dd)」の「Windows PowerShell のインストール」をご覧ください。

### -WindowStyle <Window style>
セッションのウィンドウ スタイルを設定します。 使用できる値は、Normal、Minimized、Maximized、Hidden です。

### -Command
Windows PowerShell のコマンド プロンプトに入力された場合と同様に、指定されたコマンド (および任意のパラメーター) を実行します。NoExit パラメーターが指定されていない場合は、そのまま終了します。

Command の値には、"-"、文字列、またはスクリプト ブロックを指定できます。 またはスクリプト ブロックを指定できます。 Command の値が "-" の場合、コマンド テキストは標準入力から読み取られます。

スクリプト ブロックは中かっこ ({}) で囲む必要があります。 スクリプト ブロックを指定できるのは、PowerShell.exe を Windows PowerShell で実行する場合だけです。 スクリプトの結果は、ライブ オブジェクトとしてではなく、逆シリアル化された XML オブジェクトとして親シェルに返されます。

Command の値が文字列の場合、**Command** はコマンドの最後のパラメーターでなければなりません。コマンドの後に入力された文字は、コマンド引数として解釈されるためです。

Windows PowerShell コマンドを実行する文字列を記述するには、次の形式を使用します。

```
"& {<command>}"
```

引用符によりこれが文字列であることを示し、呼び出し演算子 (&) によりコマンドが実行されます。

### -Help、-?、/?
このメッセージを表示します。 Windows PowerShell で PowerShell.exe のコマンドを入力している場合、コマンド パラメーターの前にスラッシュ (/) ではなくハイフン (-) を入力してください。 Cmd.exe では、ハイフンとスラッシュのいずれかを使用できます。

> [!NOTE]
> トラブルシューティング上の注意: Windows PowerShell 2.0 では、一部のプログラムを Windows PowerShell コンソールで開始すると、LastExitCode 0xc0000142 で失敗します。

## 例

```
PowerShell -PSConsoleFile sqlsnapin.psc1

PowerShell -Version 2.0 -NoLogo -InputFormat text -OutputFormat XML

PowerShell -Command "Get-EventLog -LogName security"

# in an existing PowerShell session that understands the curly braces mean a script block
PowerShell -Command {Get-EventLog -LogName security}

PowerShell -Command "& {Get-EventLog -LogName security}"

# To use the -EncodedCommand parameter:
$command = "dir 'c:\program files' "
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encodedCommand = [Convert]::ToBase64String($bytes)
powershell.exe -encodedCommand $encodedCommand
```




<!--HONumber=Oct16_HO1-->


