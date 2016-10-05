---
title: "ファイルとフォルダーの操作"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
ms.assetid: c0ceb96b-e708-45f3-803b-d1f61a48f4c1
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: c3f7c226fcb496e5bb51ba601429c54b43de9d52

---

# ファイルとフォルダーの操作
Windows PowerShell ドライブ間を移動したり、Windows PowerShell ドライブ上の項目を操作したりすることは、Windows の物理ディスク ドライブ上のファイルやフォルダーを操作することと似ています。 このセクションでは、ファイルとフォルダーを操作するための特定のタスクの処理方法について説明します。

### フォルダー内のすべてのファイルとフォルダーの一覧表示
**Get-ChildItem** を使用することにより、フォルダー内のすべての項目を直接取得することができます。 非表示の項目やシステム項目を表示するには、オプションの **Force** パラメーターを追加します。 たとえば、このコマンドは、Windows PowerShell ドライブ C (Windows の物理ドライブ C と同じ) に直接含まれるコンテンツを表示します。

```
Get-ChildItem -Force C:\
```

このコマンドは、Cmd.exe の **DIR** コマンドや UNIX シェルの **ls** を使用したときと同様に、直接含まれている項目のみを一覧表示します。 内包されている項目を表示するには、**Recurse** パラメーターも指定する必要があります。 (完了までにかなりの時間がかかることがあります。)C ドライブ上のすべての項目を一覧表示するには、次のように入力します。

```
Get-ChildItem -Force C:\ -Recurse
```

**Get-ChildItem** は、**Path**、**Filter**、**Include**、**Exclude** の各パラメーターを使用して項目をフィルター処理できますが、通常、これらは名前にのみ基づいています。 **Where-Object** を使用することにより、項目の他のプロパティに基づいた複雑なフィルター処理を実行できます。

次のコマンドは、Program Files フォルダー内にある、2005 年 10 月 1 日より後に最終更新され、1 メガバイト以上、10 メガバイト以下のすべての実行可能ファイルを検出します。

```
Get-ChildItem -Path $env:ProgramFiles -Recurse -Include *.exe | Where-Object -FilterScript {($_.LastWriteTime -gt "2005-10-01") -and ($_.Length -ge 1m) -and ($_.Length -le 10m)}
```

### ファイルとフォルダーのコピー
コピーは **Copy-Item** を使用して行われます。 次のコマンドは、C:\boot.ini を C:\boot.bak にバックアップします。

```
Copy-Item -Path c:\boot.ini -Destination c:\boot.bak
```

コピー先のファイルが既に存在する場合、コピー操作は失敗します。 既存のコピー先ファイルを上書きするには、Force パラメーターを使用します。

```
Copy-Item -Path c:\boot.ini -Destination c:\boot.bak -Force
```

このコマンドは、コピー先が読み取り専用である場合にも使用できます。

フォルダーのコピーも同様に動作します。 次のコマンドでは、フォルダー C:\temp\test1 を新しいフォルダー c:\temp\DeleteMe に再帰的にコピーします。

```
Copy-Item C:\temp\test1 -Recurse c:\temp\DeleteMe
```

選択した項目をコピーすることもできます。 次のコマンドでは、c:\data 内の任意の場所に格納されているすべての .txt ファイルを c:\temp\text にコピーします。

```
Copy-Item -Filter *.txt -Path c:\data -Recurse -Destination c:\temp\text
```

ファイル システムのコピー操作には、他のツールを使用することもできます。 XCOPY、ROBOCOPY、および COM オブジェクト (**Scripting.FileSystemObject** など) はすべて Windows PowerShell で動作します。 たとえば、Windows Script Host の **Scripting.FileSystem COM** クラスを使用して、C:\boot.ini を C:\boot.bak にバックアップするには、次のように入力します。

```
(New-Object -ComObject Scripting.FileSystemObject).CopyFile("c:\boot.ini", "c:\boot.bak")
```

### ファイルとフォルダーの作成
新しい項目の作成は、すべての Windows PowerShell プロバイダーで同じ方法で行われます。 Windows PowerShell プロバイダーに複数の種類の項目が含まれている場合 (たとえば、FileSystem Windows PowerShell プロバイダーではディレクトリとファイルが区別されます)、項目の種類を指定する必要があります。

次のコマンドでは、フォルダー C:\temp\New Folder が新規作成されます。

```
New-Item -Path 'C:\temp\New Folder' -ItemType "directory"
```

次のコマンドでは、空のファイル C:\temp\New Folder\file.txt が新規作成されます。

```
New-Item -Path 'C:\temp\New Folder\file.txt' -ItemType "file"
```

### フォルダー内のすべてのファイルとフォルダーの削除
内包されている項目を削除するには、**Remove-Item** を使用します。ただし、その項目に他の何らかの項目が含まれている場合、削除の確認を求められます。 たとえば、他の項目を含むフォルダー C:\temp\DeleteMe を削除しようとすると、フォルダーが削除される前に、Windows PowerShell から次のような削除の確認メッセージが表示されます。

```
Remove-Item C:\temp\DeleteMe

Confirm
The item at C:\temp\DeleteMe has children and the -recurse parameter was not
specified. If you continue, all children will be removed with the item. Are you
sure you want to continue?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help
(default is "Y"):
```

内包されている項目ごとに削除の確認が行われないようにする場合は、**Recurse** パラメーターを使用します。

```
Remove-Item C:\temp\DeleteMe -Recurse
```

### Windows のアクセス可能なドライブとしてのローカル フォルダーのマッピング
**subst** コマンドを使用することにより、ローカル フォルダーをマッピングすることもできます。 次のコマンドは、ローカルの Program Files ディレクトリをルートとするローカル ドライブ P: を作成します。

```
subst p: $env:programfiles
```

ネットワーク ドライブと同様に、**subst** を使用して Windows PowerShell 内でマッピングされたドライブは、直ちに Windows PowerShell シェルに表示されます。

### 配列へのテキスト ファイルの読み込み
テキスト データの一般的な格納形式の 1 つに、個々の行を個別のデータ要素として扱うファイルを使用する方法があります。 **Get-Content** コマンドレットを使用することにより、1 つのステップでファイル全体を読み取ることができます。以下にその例を示します。

```
PS> Get-Content -Path C:\boot.ini
[boot loader]
timeout=5
default=multi(0)disk(0)rdisk(0)partition(1)\WINDOWS
[operating systems]
multi(0)disk(0)rdisk(0)partition(1)\WINDOWS="Microsoft Windows XP Professional"
 /noexecute=AlwaysOff /fastdetect
multi(0)disk(0)rdisk(0)partition(1)\WINDOWS=" Microsoft Windows XP Professional 
with Data Execution Prevention" /noexecute=optin /fastdetect
```

**Get-Content** は、ファイルから読み取ったデータを、1 行のファイル内容につき 1 つの要素を含む配列として扱います。 これを確認するには、返された内容の **Length** をチェックします。

```
PS> (Get-Content -Path C:\boot.ini).Length
6
```

このコマンドは、情報の一覧を Windows PowerShell に直接取得するときに最も役立ちます。 たとえば、コンピューター名または IP アドレスの一覧を、ファイルの各行に 1 つの名前を入力して C:\temp\domainMembers.txt ファイルに保存できます。 **Get-Content** を使用してファイルの内容を取得し、変数 **$Computers** に格納するには、次のように入力します。

```
$Computers = Get-Content -Path C:\temp\DomainMembers.txt
```

**$Computers** には、各要素に 1 つのコンピューター名が含まれた配列が格納されます。




<!--HONumber=Oct16_HO1-->


