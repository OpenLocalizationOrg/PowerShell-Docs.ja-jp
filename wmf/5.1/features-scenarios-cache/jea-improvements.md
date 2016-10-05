---
title: "Just Enough Administration の強化"
contributor: ryanpu
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 7d2f293f000d3d82f4a227d3b3760988d9f02be7

---

## JEA エンドポイントとの間の制約付きのファイル コピー

JEA エンドポイントとの間でファイルをリモートでコピーできるようになりました。接続ユーザーはシステム上のファイルを*どれでも*コピーできるわけではないので安心することができます。
これは、接続ユーザー用のユーザー ドライブをマウントするよう PSSC ファイルを構成することによって可能になります。
ユーザー ドライブは、各接続ユーザーに固有の新しい PSDrive であり、複数のセッションにわたって保持されます。
Copy-Item を使用して JEA セッションとの間でファイルのコピーを行うと、該当するユーザー ドライブへのアクセスしか許可しないように制約が課せられます。
その他の PSDrive ファイルへのコピーを試みると失敗します。

JEA セッションの構成ファイルで、ユーザー ドライブを設定するには、次の新しいフィールドを使用します。
```powershell
MountUserDrive = $true
UserDriveMaximumSize = 10485760    # 10 MB
```

ユーザーのドライブをバックアップ フォルダーが作成されます。 `$env:LOCALAPPDATA\Microsoft\Windows\PowerShell\DriveRoots\DOMAIN_USER`

ユーザー ドライブを公開するように構成された JEA エンドポイントとの間で、ユーザー ドライブを使用してファイルのコピーを行うには、Copy-Item で `-ToSession` および `-FromSession` パラメーターを使用します。
```powershell
# Connect to the JEA endpoint
$jeasession = New-PSSession -ComputerName 'srv01' -ConfigurationName 'UserDemo'

# Copy a file in the local folder to the remote machine.
# Note: you cannot specify the file name or subfolder on the remote machine. You must exactly type "User:"
Copy-Item -Path .\SampleFile.txt -Destination User: -ToSession $jeasession

# Copy the file back from the remote machine to your local machine
Copy-Item -Path User:\SampleFile.txt -Destination . -FromSession $jeasession
```

ユーザー ドライブに格納されたデータを処理して、ロール機能ファイル内のユーザーがそれらを使用できるようにするためのカスタム関数を作成することができます。

## グループの管理されたサービス アカウントのサポート

場合によって、JEA セッションでユーザーが実行する必要があるタスクは、ローカル コンピューター以外のリソースにアクセスすることが必要な場合があります。
JEA セッションが仮想アカウントを使用するように構成されている場合、そのようなリソースへのアクセスの試みは、仮想アカウントまたは接続ユーザーからではなく、ローカル コンピューターの ID からのアクセスのように見えます。
TP5 で有効にしたので、アカウントのコンテキストで、[グループ管理サービス] (https://technet.microsoft.com/en-us/library/jj128431 (v=ws.11\).aspx) ドメイン id を使用してネットワーク リソースにアクセスするはるかに簡単になります JEA を実行するためのサポート。

JEA セッションを、gMSA アカウントの下で実行されるように構成するには、PSSC ファイルで次の新しいキーを使用します。
```powershell
# Provide the name of your gMSA account here (don't include a trailing $)
# The local machine must be privileged to use this gMSA in Active Directory
GroupManagedServiceAccount = 'myGMSAforJEA'

# You cannot configure a JEA endpoint to use both a gMSA and virtual account
# You can leave the RunAsVirtualAccount field commented out or explicitly set it to false
RunAsVirtualAccount = $false
```

> **注:** グループの管理されたサービス アカウントでは、仮想アカウントの分離または限定的な範囲を許容していません。
> 接続ユーザーはすべて同じ gMSA ID を共有することになります。この ID は企業全体を対象とするアクセス許可を持つ場合もあります。
> gMSA の使用を選択する場合は細心の注意を払ってください。可能であればローカル コンピューターに限定された仮想アカウントを常に優先してください。

## 条件付きのアクセス ポリシー

あるユーザーがシステムの管理を目的としてシステムに接続したときにその人が実行できる操作を制限する場合に JEA は便利です。しかし、だれかが JEA を使用できる*タイミング*を制限したい場合はどうするのですか?
ユーザーが JEA セッションを確立するときに属する必要があるセキュリティ グループを指定できるように、セッション構成ファイル (.pssc) に構成オプションを追加しました。
環境内にジャスト イン タイム (JIT) システムが搭載されていて、高い権限を持つ JEA エンドポイントにアクセスする前にユーザーに自分の権限を昇格させる必要があるとき、このオプションは特に有用です。

PSSC ファイル内の新しい *RequiredGroups* フィールドを使用すると、ユーザーが JEA に接続できるかどうかを判断するロジックを指定することができます。
このロジックでは、ルールを構築するために "And" キーと "Or" キーを使用するハッシュ テーブル (必要に応じて入れ子になっている) を指定します。
このフィールドの活用方法について、次に例を示します。
```powershell
# Example 1: Connecting users must belong to a security group called "elevated-jea"
RequiredGroups = @{ And = 'elevated-jea' }

# Example 2: Connecting users must have signed on with 2 factor authentication or a smart card
# The 2 factor authentication group name is "2FA-logon" and the smart card group name is "smartcard-logon"
RequiredGroups = @{ Or = '2FA-logon', 'smartcard-logon' }

# Example 3: Connecting users must elevate into "elevated-jea" with their JIT system and have logged on with 2FA or a smart card
RequiredGroups = @{ And = 'elevated-jea', @{ Or = '2FA-logon', 'smartcard-logon' }}
```

## 固定: Windows Server 2008 R2 で仮想アカウントがサポートされるようになりました。
WMF 5.1 では、Windows Server 2008 R2 で仮想アカウントを使用できるようになりました。これにより、Windows Server 2008 R2 - 2016 にわたり一貫した構成と機能の類似性が提供されます。
Windows 7 で JEA を使用する場合、仮想アカウントはまだサポートされていません。


<!--HONumber=Oct16_HO1-->


