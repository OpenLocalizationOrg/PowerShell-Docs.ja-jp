---
title: "レジストリ キーの操作"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: 91bfaecd-8684-48b4-ad86-065dfe6dc90a
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 4809eb60ba1a5529343c2ab3c88493bf2c32389b

---

# レジストリ キーの操作
レジストリ キーは Windows PowerShell ドライブ上の項目であるため、それらの操作はファイルやフォルダーの操作によく似ています。 1 つの決定的な違いは、レジストリ ベースの Windows PowerShell ドライブ上のすべての項目は、ファイル システム ドライブ上のフォルダーと同じように、コンテナーであることです。 ただし、レジストリ エントリとそれに関連する値は、個別の項目ではなく、項目のプロパティです。

### レジストリ キーのすべてのサブキーを一覧表示
**Get-ChildItem** を使用することにより、レジストリ キーの直下にあるすべての項目を表示することができます。 非表示の項目やシステム項目を表示するには、オプションの **Force** パラメーターを追加します。 たとえば、次のコマンドは、HKEY_CURRENT_USER レジストリ ハイブに対応する、Windows PowerShell ドライブ HKCU: の直下にある項目を表示します。

```
PS> Get-ChildItem -Path hkcu:\

   Hive: Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER

SKC  VC Name                           Property
---  -- ----                           --------
  2   0 AppEvents                      {}
  7  33 Console                        {ColorTable00, ColorTable01, ColorTab...
 25   1 Control Panel                  {Opened}
  0   5 Environment                    {APR_ICONV_PATH, INCLUDE, LIB, TEMP...}
  1   7 Identities                     {Last Username, Last User ...
  4   0 Keyboard Layout                {}
...
```

これらは、レジストリ エディター (Regedit.exe) 内の HKEY_CURRENT_USER の下に表示される最上位キーです。

レジストリ プロバイダーの名前とその後に "**::**" を指定することにより、このレジストリ パスを指定することもできます。 レジストリ プロバイダーの完全名は **Microsoft.PowerShell.Core\Registry** ですが、短縮して **Registry** だけにすることができます。 次のいずれのコマンドを使用しても、HKCU の直下にある内容を一覧表示できます。

```
Get-ChildItem -Path Registry::HKEY_CURRENT_USER
Get-ChildItem -Path Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER
Get-ChildItem -Path Registry::HKCU
Get-ChildItem -Path Microsoft.PowerShell.Core\Registry::HKCU
Get-ChildItem HKCU:
```

これらのコマンドは、Cmd.exe の **DIR** コマンドや UNIX シェルの **ls** を使用したときと同様に、直接含まれている項目のみ一覧表示します。 内包されている項目を表示するには、**Recurse** パラメーターを指定する必要があります。 HKCU 内のすべてのレジストリ キーを一覧表示するには、次のコマンドを使用します (この操作は、非常に長い時間がかかることがあります)。

```
Get-ChildItem -Path hkcu:\ -Recurse
```

**Get-ChildItem** は、**Path**、**Filter**、**Include**、**Exclude** の各パラメーターを使用して複雑なフィルター処理機能を実行できますが、これらのパラメーターは通常、名前にのみ基づいています。 **Where-Object** コマンドレットを使用することにより、項目の他のプロパティに基づいた複雑なフィルター処理を実行できます。 次のコマンドは、1 つ以内のサブキーと 4 つの値を持つ、HKCU:\Software 内のすべてのキーを検索します。

```
Get-ChildItem -Path HKCU:\Software -Recurse | Where-Object -FilterScript {($_.SubKeyCount -le 1) -and ($_.ValueCount -eq 4) }
```

### キーのコピー
コピーは **Copy-Item** を使用して行われます。 次のコマンドは、HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion とそのすべてのプロパティを HKCU:\ にコピーして、"CurrentVersion" という名前の新しいキーを作成します。

```
Copy-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion' -Destination hkcu:
```

レジストリ エディター内、または **Get-ChildItem** を使用してこの新しいキーを調べると、格納されているサブキーのコピーが新しい場所に存在しないことがわかります。 コンテナーのすべての内容をコピーするために、**Recurse** パラメーターを指定する必要があります。 上記のコピー コマンドを再帰的に実行するには、次のコマンドを使用します。

```
Copy-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion' -Destination hkcu: -Recurse
```

ファイル システムのコピーは、使用可能な他の既存のツールを使用して行うこともできます。 任意のレジストリ編集ツール (reg.exe、regini.exe、regedit.exe など) およびレジストリの編集をサポートする COM オブジェクト (WScript.Shell や WMI の StdRegProv クラスなど) を Windows PowerShell 内から使用できます。

### キーの作成
レジストリでのキーの新規作成は、ファイル システムに新しい項目を作成するよりも簡単です。 すべてのレジストリ キーはコンテナーであるため、項目の種類を指定する必要はなく、次に示すように、明示的なパスを指定するだけで済みます。

```
New-Item -Path hkcu:\software_DeleteMe
```

また、プロバイダーに基づくパスを使用して、キーを指定することもできます。

```
New-Item -Path Registry::HKCU_DeleteMe
```

### キーの削除
項目の削除は、基本的にすべてのプロバイダーで同じです。 次のコマンドを実行すると、確認を求められずに項目が削除されます。

```
Remove-Item -Path hkcu:\Software_DeleteMe
Remove-Item -Path 'hkcu:\key with spaces in the name'
```

### 特定のキーの下にあるすべてのキーの削除
内包されている項目を削除するには、**Remove-Item** を使用します。ただし、その項目に他の何らかの項目が含まれている場合は、削除の確認を求められます。 たとえば、作成した HKCU:\CurrentVersion サブキーを削除しようとしすると、次のメッセージが表示されます。

```
Remove-Item -Path hkcu:\CurrentVersion

Confirm
The item at HKCU:\CurrentVersion\AdminDebug has children and the -recurse
parameter was not specified. If you continue, all children will be removed with
 the item. Are you sure you want to continue?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):
```

確認のメッセージを表示せずに、内包されている項目を削除するには、**-Recurse** パラメーターを指定します。

```
Remove-Item -Path HKCU:\CurrentVersion -Recurse
```

HKCU:\CurrentVersion 内のすべての項目を削除するものの、HKCU:\CurrentVersion そのものは削除しない場合は、代わりに次のように入力します。

```
Remove-Item -Path HKCU:\CurrentVersion\* -Recurse
```




<!--HONumber=Oct16_HO1-->


