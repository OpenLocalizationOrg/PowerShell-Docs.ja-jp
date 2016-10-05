---
title: "DSC リソース メソッドの直接呼び出し"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 1fe624c2532e44ed675762f3c141934fb4f0b60d

---

# DSC リソース メソッドの直接呼び出し

>適用先: Windows PowerShell 5.0

[Invoke DscResource](https://technet.microsoft.com/en-us/library/mt517869.aspx) コマンドレットを使用すると、DSC リソースの関数やメソッド (MOF ベースのリソースの **Get-TargetResource**、**Set-TargetResource**、**Test-TargetResource** 関数、またはクラスベースのリソースの **Get**、**Set**、**Test** メソッド) を直接呼び出すことができます。 これは、DSC リソースを使用するサード パーティが使用したり、リソースの開発中に役立つツールとして利用したりできます。 

通常このコマンドレットは、メタ構成プロパティ `refreshMode = 'Disabled'` と組み合わせて使用されますが、どの **refreshMode** が設定されているかに関係なく使用できます。

**Invoke-DscResource** コマンドレットを呼び出すときに、**Method** パラメーターを使用して、呼び出すメソッドまたは関数を指定します。 リソースのプロパティを指定するには、ハッシュ テーブルを、**Property** パラメーターの値として渡します。

リソースのメソッドを直接呼び出す例を次に示します。

## ファイルが存在することを確認します

```powershell
$result = Invoke-DscResource -Name File -Method Set -Property @{
                            DestinationPath = "$env:SystemDrive\\DirectAccess.txt";
                            Contents = 'This file is create by Invoke-DscResource'} -Verbose
$result | fl
```

## ファイルが存在することをテストします

```powershell
$result = Invoke-DscResource -Name File -Method Test -Property @{
                            DestinationPath="$env:SystemDrive\\DirectAccess.txt";
                            Contents='This file is create by Invoke-DscResource'} -Verbose
$result | fl
```

## ファイルの内容を取得します

```powershell
$result = Invoke-DscResource -Name File -Method Get -Property @{
                            DestinationPath="$env:SystemDrive\\DirectAccess.txt";
                            Contents='This file is create by Invoke-DscResource'} -Verbose
$result.ItemValue | fl
```

>**注:** 複合リソースのメソッドを直接呼び出すことはできません。 代わりに、複合リソースの基になるリソースのメソッドを呼び出してください。

## 参照
- [MOF を使用したカスタム DSC リソースの記述](authoringResourceMOF.md) 
- [PowerShell クラスを使用したカスタム DSC リソースの記述](authoringResourceClass.md)
- [DSC リソースをデバッグします。](debugResource.md)




<!--HONumber=Oct16_HO1-->


