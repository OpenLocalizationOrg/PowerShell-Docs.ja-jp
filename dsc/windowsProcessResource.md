---
title: "DSC WindowsProcess リソース"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 0fe5e7d9679d44bb50c897badf8c6517b95049e2

---

# DSC WindowsProcess リソース

> 適用先: Windows PowerShell 4.0、Windows PowerShell 5.0

Windows PowerShell Desired State Configuration (DSC) の **WindowsProcess** リソースは、ターゲット ノードにプロセスを構成するためのメカニズムを備えています。

## 構文

```
WindowsProcess [string] #ResourceName
{
    Arguments = [string]
    Path = [string]
    [ Credential = [PSCredential] ]
    [ Ensure = [string] { Absent | Present }  ]
    [ DependsOn = [string[]] ]
    [ StandardErrorPath = [string] ]
    [ StandardInputPath = [string] ]
    [ StandardOutputPath = [string] ]
    [ WorkingDirectory = [string] ]
}
```

## プロパティ
|  プロパティ  |  説明   | 
|---|---| 
| 引数| プロセスに渡す引数の文字列をそのまま示します。 複数の引数を渡す必要がある場合は、そのすべてをこの文字列内に配置します。| 
| パス| プロセスの実行可能ファイルのパス。 これが実行可能ファイルの名前の場合 (完全修飾パスではない)、DSC リソースは環境**パス**変数 (`$env:Path`) を検索し、ファイルを見つけます。 このプロパティの値が完全修飾パスの場合、DSC は**パス**環境変数でファイルを探すことはせず、パスが存在しない場合はエラーをスローします。 相対パスは指定できません。| 
| Credential| プロセスを開始するための資格情報を示します。| 
| Ensure| プロセスが存在するかどうかを示します。 プロセスが存在することを保証するには、このプロパティを "Present" に設定します。 それ以外の場合は、"Absent" に設定します。 既定は "Present" です。| 
| DependsOn | このリソースを構成する前に、他のリソースの構成を実行する必要があることを示します。 たとえば、最初に実行するリソース構成スクリプト ブロックの ID が __ResourceName__ で、そのタイプが __ResourceType__ である場合、このプロパティを使用する構文は DependsOn = "[ResourceType]ResourceName" になります。| 
| StandardErrorPath| 標準エラーを書き込むディレクトリ パスを示します。 既存のファイルは上書きされます。| 
| StandardInputPath| 標準入力の場所を示します。| 
| StandardOutputPath| 標準出力の書き込み場所を示します。 既存のファイルは上書きされます。| 
| WorkingDirectory| プロセスの現在の作業ディレクトリとして使用される場所を示します。| 




<!--HONumber=Oct16_HO1-->


