---
title: "DSC ProcessSet リソース"
ms.date: 2016-05-23
keywords: powershell DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 012a0e5c4f2a1f60ecea869d588b9c54e0567ced

---

# DSC WindowsProcess リソース

> 適用先: Windows PowerShell 5.0

Windows PowerShell Desired State Configuration (DSC) の **ProcessSet** リソースは、ターゲット ノードにプロセスを構成するためのメカニズムを備えています。 このリソースは[複合リソース](authoringResourceComposite.md)であり、`GroupName` パラメーターに指定されているグループごとに [WindowsProcess リソース](windowsProcessResource.md)を呼び出します。

## 構文

```
WindowsProcess [string] #ResourceName
{
    Arguments = [string]
    Path = [string]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ StandardOutputPath = [string] ]
    [ StandardErrorPath = [string] ]
    [ StandardInputPath = [string] ]   
    [ WorkingDirectory = [string] ]
    [ DependsOn = [string[]] ]
}
```

## プロパティ
|  プロパティ  |  説明   | 
|---|---| 
| 引数| 現状のままプロセスに渡す引数の文字列。 複数の引数を渡す必要がある場合は、そのすべてをこの文字列内に配置します。| 
| パス| プロセスの実行可能ファイルへのパス。 実行可能ファイルの名前がある場合 (完全修飾パスではない)、DSC リソースは環境**パス**変数 (`$env:Path`) を検索し、ファイルを見つけます。 このプロパティの値が完全修飾パスの場合、DSC は**パス**環境変数でファイルを探すことはせず、パスが存在しない場合はエラーをスローします。 相対パスは指定できません。| 
| Credential| プロセスを開始するための資格情報を示します。| 
| Ensure| プロセスが存在するかどうかを指定します。 プロセスが存在することを保証するには、このプロパティを "Present" に設定します。 それ以外の場合は、"Absent" に設定します。 既定は "Present" です。| 
| StandardErrorPath| プロセスにより標準エラーが書き込まれるパス。 既存のファイルは上書きされます。| 
| StandardInputPath| プロセスが標準入力を受け取るとき、その発信元となるストリーム。| 
| StandardOutputPath| プロセスにより標準出力が書き込まれるファイルのパス。 既存のファイルは上書きされます。| 
| WorkingDirectory| プロセスの現在の作業ディレクトリとして使用されている場所。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が **ResourceName** で、そのタイプが **_ResourceType** である場合、このプロパティを使用する構文は DependsOn = "[ResourceType]ResourceName" になります。| 




<!--HONumber=Oct16_HO1-->


