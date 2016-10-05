---
title: "コマンドに関する情報の取得"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 56f8e5b4-d97c-4e59-abbe-bf13e464eb0d
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: c60f0f63b0d64c82a3ae0716743087ee2b8740c0

---

# コマンドに関する情報の取得
Windows PowerShell の **Get-Command** コマンドレットは、現在のセッションで使用できるすべてのコマンドを取得します。 Windows PowerShell プロンプトで **Get-Command** と入力すると、次のような出力が得られます。

```
PS> Get-Command
CommandType     Name                            Definition
-----------     ----                            ----------
Cmdlet          Add-Content                     Add-Content [-Path] <String[...
Cmdlet          Add-History                     Add-History [[-InputObject] ...
Cmdlet          Add-Member                      Add-Member [-MemberType] <PS...
...
```

この出力は、Cmd.exe のヘルプの出力に似ている、内部コマンドの表形式の概要です。 上記の **Get-Command** コマンドの出力の抜粋では、表示されているすべてのコマンドのコマンドレットは CommandType です。 コマンドレットは、Windows PowerShell の組み込みコマンドの種類であり、Cmd.exe の **dir** コマンドと **cd** コマンドや、BASH などの UNIX シェルの組み込み要素にほぼ対応します。

**Get-Command** コマンドの出力では、すべての定義が省略記号 (...) で終わっており、PowerShell で使用可能なスペースにすべてのコンテンツを表示できないことを示します。 Windows PowerShell に出力が表示されると、出力をテキストとして書式設定し、ウィンドウにきちんと収まるようにデータを配置します。 これについては、フォーマッタのセクションで後述します。

**Get-Command** コマンドレットには、各コマンドレットの構文を取得する **Syntax** パラメーターがあります。 Get-help コマンドレットの構文を取得するには、次のコマンドを使用します。

**Get-help の get コマンドの構文**

```
Get-Help [[-Name] <String>] [-Path <String>] [-Category <String[]>] [-Component <String[]>] [-Functionality <String[]>]
 [-Role <String[]>] [-Full] [-Online] [-Verbose] [-Debug] [-ErrorAction <ActionPreference>] [-WarningAction <ActionPreference>] [-ErrorVariable <String>] [-WarningVariable <String>] [-OutVariable <String>] [-OutBuffer <Int32>]

Get-Help [[-Name] <String>] [-Path <String>] [-Category <String[]>] [-Component <String[]>] [-Functionality <String[]>]
 [-Role <String[]>] [-Detailed] [-Online] [-Verbose] [-Debug] [-ErrorAction <ActionPreference>] [-WarningAction <ActionPreference>] [-ErrorVariable <String>] [-WarningVariable <String>] [-OutVariable <String>] [-OutBuffer <Int32>]

Get-Help [[-Name] <String>] [-Path <String>] [-Category <String[]>] [-Component <String[]>] [-Functionality <String[]>]
 [-Role <String[]>] [-Examples] [-Online] [-Verbose] [-Debug] [-ErrorAction <ActionPreference>] [-WarningAction <ActionPreference>] [-ErrorVariable <String>] [-WarningVariable <String>] [-OutVariable <String>] [-OutBuffer <Int32>]

Get-Help [[-Name] <String>] [-Path <String>] [-Category <String[]>] [-Component <String[]>] [-Functionality <String[]>]
 [-Role <String[]>] [-Parameter <String>] [-Online] [-Verbose] [-Debug] [-ErrorAction <ActionPreference>] [-WarningAction <ActionPreference>] [-ErrorVariable <String>] [-WarningVariable <String>] [-OutVariable <String>] [-OutBuffer <Int32>]
```

### 使用可能なコマンドの種類の表示
**Get-Command** コマンドは、Windows PowerShell で使用可能なコマンドのすべてを一覧表示しません。 代わりに、**Get-Command** コマンドは、現在のセッションのコマンドレットのみを一覧表示します。 Windows PowerShell は、実際にはその他のいくつかの種類のコマンドをサポートします。 エイリアス、関数、およびスクリプトも Windows PowerShell のコマンドですが、『Windows PowerShell ユーザー ガイド』では詳細に説明していません。 実行可能な外部ファイル、または登録されているファイル タイプのハンドラーを持つ外部ファイルも、コマンドとして分類されます。

セッション内のすべてのコマンドを取得するには、次のように入力します。

```
Get-Command *
```

この一覧には、検索パスの外部ファイルが含まれるため、何千もの項目が含まれることがあります。 コマンド セットを削減して表示する方が実用的です。

その他の種類のネイティブ コマンドを取得するには、**Get-Command** コマンドレットの **CommandType** パラメーターを使用します。

> [!NOTE]
> アスタリスク (*) は、Windows PowerShell コマンドの引数のワイルドカードによるマッチングに使用されます。 * は、「1 つ以上の任意の文字に一致する」という意味です。 入力する **Get-command a \ &#42;** という文字で始まるすべてのコマンドを検索する"a"です。 Cmd.exe のワイルドカードのマッチングとは異なり、Windows PowerShell のワイルドカードは、ピリオドもマッチングします。

コマンドの割り当て済みのニックネームであるコマンドのエイリアスを取得するには、次のように入力します。

```
Get-Command -CommandType Alias
```

現在のセッションの関数を取得するには、次のように入力します。

```
Get-Command -CommandType Function
```

Windows PowerShell の検索パス内のスクリプトを表示するには、次のように入力します。

```
Get-Command -CommandType Script
```




<!--HONumber=Oct16_HO1-->


