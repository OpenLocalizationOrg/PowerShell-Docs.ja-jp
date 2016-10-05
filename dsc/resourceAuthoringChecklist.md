---
title: "リソース作成のチェックリスト"
ms.date: 2016-07-11
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: b2900987b1102cf41880e5af0a0cc44bc6499ef5

---

# リソース作成のチェックリスト
このチェックリストは、新しい DSC リソースを作成するときのベスト プラクティスの一覧です。
## リソース モジュールにすべてのリソースの .psd1 ファイルと schema.mof が含まれている 
リソースが正しい構造であり、必要なすべてのファイルが含まれていることを確認します。 すべてのリソース モジュールには .psd1 ファイルが含まれている必要があり、すべて非複合リソースには .schema.mof ファイルが含まれている必要があります。 スキーマが含まれていないリソースは **Get-DscResource** によって一覧表示されず、ユーザーは ISE でこれらのモジュールに対してコードを記述するときに IntelliSense を使用できません。 [xPSDesiredStateConfiguration リソース モジュール](https://github.com/PowerShell/xPSDesiredStateConfiguration)の一部である xRemoteFile リソースのディレクトリ構造は、次のようになります。


```
xPSDesiredStateConfiguration
    DSCResources
        MSFT_xRemoteFile
            MSFT_xRemoteFile.psm1
            MSFT_xRemoteFile.schema.mof
    Examples
        xRemoteFile_DownloadFile.ps1
    ResourceDesignerScripts
        GenerateXRemoteFileSchema.ps1
    Tests
        ResourceDesignerTests.ps1
    xPSDesiredStateConfiguration.psd1
```

## リソースとスキーマが正しい##
リソース スキーマ (*.schema.mof) ファイルを確認します。 [DSC リソース デザイナー](https://www.powershellgallery.com/packages/xDSCResourceDesigner/)をスキーマの開発と試験に利用できます。 次のことを確認します。
- プロパティの型が正しい (たとえば、数値を受け入れるプロパティには文字列を使用せず、UInt32 またはその他の数値型を代わりに使用する必要があります)
- プロパティの属性が正しく指定されている ([key]、[required]、[write]、[read])
- スキーマ内の少なくとも 1 つのパラメーターが [key] としてマークされている必要があります。
- [read] プロパティは [required]、[key]、[write] のいずれとも共存しません。
- [read] 以外の複数の条件が指定されている場合、[key] が優先されます。
- [write] と [required] が指定されている場合、[required] が優先されます。
- ValueMap が適切な場所に指定されている

次に例を示します。
```
[Read, ValueMap{"Present", "Absent"}, Values{"Present", "Absent"}, Description("Says whether DestinationPath exists on the machine")] String Ensure;
```

- フレンドリ名が指定され、DSC 名前付け規則に準拠している

例: ```[ClassVersion("1.0.0.0"), FriendlyName("xRemoteFile")]```

- すべてのフィールドにわかりやすい説明があります。 PowerShell GitHub リポジトリには、[xRemoteFile の .schema.mof](https://github.com/PowerShell/xPSDesiredStateConfiguration/blob/dev/DSCResources/MSFT_xRemoteFile/MSFT_xRemoteFile.schema.mof) など、良い例があります。

また、[DSC リソース デザイナー](https://www.powershellgallery.com/packages/xDSCResourceDesigner/)から **Test-xDscResource** および **Test-xDscSchema** コマンドレットを使用して、リソースとスキーマを自動的に確認する必要があります。
```
Test-xDscResource <Resource_folder>
Test-xDscSchema <Path_to_resource_schema_file>
```
たとえば、次のように入力します。
```powershell
Test-xDscResource ..\DSCResources\MSFT_xRemoteFile
Test-xDscSchema ..\DSCResources\MSFT_xRemoteFile\MSFT_xRemoteFile.schema.mof
```

## リソースがエラーなしで読み込まれる ##
リソース モジュールを正常に読み込めるか確認します。
これは、手動で実行する (`Import-Module <resource_module> -force ` を実行してエラーが発生しないことを確認する) か、自動テストを作成して実行することができます。 後者の場合は、テスト ケースで次の構造に従うことができます。
```powershell
$error = $null
Import-Module <resource_module> –force
If ($error.count –ne 0) {
    Throw “Module was not imported correctly. Errors returned: $error”
}
```
## 正の場合、リソースはべき等である 
DSC リソースの基本的な特性の 1 つにべき等性があります。 つまり、そのリソースを含む DSC 構成を複数回適用したとき、常に同じ結果が得られます。 たとえば、次の File リソースを含む構成を作成するとします。
```powershell
File file {
    DestinationPath = "C:\test\test.txt"
    Contents = "Sample text"
} 
```
これを最初に適用した後、test.txt ファイルが C:\test フォルダーに出現します。 ただし、同じ構成の後続の実行で、マシンの状態を変更しないでください (たとえば、test.txt ファイルのコピーを作成しないでください)。
リソースがべき等であることを確認するには、リソースを直接テストする場合は **Set-TargetResource** を繰り返し呼び出し、エンド ツー エンド テストを実行する場合は **Start-DscConfiguration** を複数回呼び出します。 実行するたびに結果が同じである必要があります。 


## ユーザー変更シナリオのテスト ##
コンピューターの状態を変更し、DSC を再実行することで、**Set-TargetResource** と **Test-TargetResource** が適切に機能することを確認できます。 実行する手順を次に示します。
1.  目的の状態でないリソースで開始します。
2.  リソースで構成を実行します。
3.  **Test-DscConfiguration** が true を返すことを確認します。
4.  構成対象アイテムが目的の状態から抜けるように変更します。
5.  **Test-DscConfiguration** が false を返すことを確認します。Registry リソースを使用した具体的な例を次に示します。
1.  目的の状態でないレジストリ キーで開始します。
2.  **Start-DscConfiguration** を構成で実行し、目的の状態にして正常終了することを確認します。
3.  **Test-DscConfiguration** を実行し、true を返すことを確認します。
4.  キーの値を変更して、目的でない状態にします。
5.  **Test-DscConfiguration** を実行し、false を返すことを確認します。
6.  Get-DscConfiguration を使用して Get-TargetResource 機能が検証されている

Get-TargetResource は、リソースの現在の状態の詳細を返す必要があります。 構成を適用した後に Get-DscConfiguration を呼び出し、出力がマシンの現在の状態を正しく反映していることを検証することによってテストします。 この領域の問題は Start-DscConfiguration の呼び出し時には出現しないため、個別にテストすることが重要です。

## **Get/Set/Test-TargetResource** 関数を直接呼び出す ##

リソースに実装されている **Get/Set/Test-TargetResource** 関数をテストするには、それらを直接呼び出し、期待どおりに動作することを確認します。

## **Start-DscConfiguration** を使用してエンド ツー エンドで確認する ##

直接呼び出すことによって **Get/Set/Test-TargetResource** 関数をテストすることは重要ですが、この方法ですべての問題が検出されるわけではありません。 テストにおいては、**Start-DscConfiguration** やプル サーバーの使用に関する部分を重視する必要があります。 これはユーザーが実際にリソースを使用する方法であり、この種類のテストの重要性を過小評価しないようにしてください。 可能性がある問題の種類:
- DSC エージェントはサービスとして実行されるため、資格情報またはセッションの動作が異なる可能性があります。  機能は必ずエンド ツー エンドでテストしてください。
- **Start-DscConfiguration** によるエラー出力は、**Set-TargetResource** 関数を直接呼び出したときに表示されるものとは異なる場合があります。

## すべての DSC 対応プラットフォームで互換性を試験する ##
リソースは、DSC がサポートされているすべてのプラットフォーム (Windows Server 2008 R2 以降) で動作する必要があります。 DSC の最新バージョンを取得するには、OS に最新の WMF (Windows Management Framework) をインストールします。 リソースがこれらのプラットフォームの一部で動作しないことが意図的である場合は、特定のエラー メッセージが返される必要があります。 また、リソースで、呼び出すコマンドレットが特定のマシン上に存在するかどうかがチェックされることも確認します。 Windows Server 2012 には、WMF がインストールされていても Windows Server 2008R2では使用できない多数の新しいコマンドレットが追加されています。 

## (該当する場合) Windows クライアントで確認する ##
非常に一般的なテスト不足の 1 つは、Windows のサーバー バージョンでのみリソースを検証することです。 多くのリソースはクライアント SKU でも動作するように設計されているため、その場合は、これらのプラットフォームでも必ずテストします。 
## Get-DscResource でリソースが一覧表示される ##
モジュールを展開した後、Get-DscResource を呼び出すと、結果として他のリソースが一覧表示される必要があります。 一覧にリソースが見つからない場合は、そのリソースの schema.mof ファイルが存在することを確認します。 
## リソース モジュールに例が含まれている ##
他のユーザーが使い方を理解できるように質の高いサンプルを作成します。 多くのユーザーはサンプル コードをドキュメントとして扱うため、特に、このことは重要です。 
- 最初に、モジュールに含める例を決定する必要があります。少なくとも、リソースの最も重要なユース ケースを反映する必要があります。
- エンド ツー エンドのシナリオで連携して動作する必要がある複数のリソースがモジュールに含まれる場合、基本的なエンド ツー エンドの例を最初に配置することをお勧めします。
- 最初の例は、非常に単純なもの - 小さい管理しやすいチャンク内のリソースを使い始める方法 (新しい VHD の作成など) にする必要があります。
- その後の例は、これらの例の上にビルドし (VHD からの VM の作成、VM の削除、VM の変更など)、高度な機能を示します (動的メモリがある VM の作成など)。
- 構成の例はパラメーター化する必要があります (すべての値は構成にパラメーターとして渡す必要があり、ハードコードされた値は使用しません)。
```powershell
configuration Sample_xRemoteFile_DownloadFile
{
    param
    (
        [string[]] $nodeName = 'localhost',

        [parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [String] $destinationPath,

        [parameter(Mandatory = $true)]
        [ValidateNotNullOrEmpty()]
        [String] $uri,

        [String] $userAgent,

        [Hashtable] $headers
    )

    Import-DscResource -Name MSFT_xRemoteFile -ModuleName xPSDesiredStateConfiguration

    Node $nodeName
    {
        xRemoteFile DownloadFile
        {
            DestinationPath = $destinationPath
            Uri = $uri
            UserAgent = $userAgent
            Headers = $headers
        }
    }
} 
```
- スクリプトの例の最後に、実際の値を使用して構成を呼び出す方法の例 (コメント アウト) を含めることをお勧めします。 たとえば、上記の構成では、UserAgent を指定する最善の方法が次のとおりであることは必ずしも明白ではありません。

`UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::InternetExplorer`  
その場合、その構成で意図する実行内容をコメントで明らかにできます。
```
<# 
Sample use (parameter values need to be changed according to your scenario):

Sample_xRemoteFile_DownloadFile -destinationPath "$env:SystemDrive\fileName.jpg" -uri "http://www.contoso.com/image.jpg"

Sample_xRemoteFile_DownloadFile -destinationPath "$env:SystemDrive\fileName.jpg" -uri "http://www.contoso.com/image.jpg" `
-userAgent [Microsoft.PowerShell.Commands.PSUserAgent]::InternetExplorer -headers @{"Accept-Language" = "en-US"}
#>  
```
- 各例で、実行内容を示す簡単な説明と、パラメーターの意味を記述します。 
- リソースのほとんどの重要なシナリオが例でカバーされていることを確認し、不足がない場合は、すべてが実行され、マシンを目的の状態にすることを検証します。  

## エラー メッセージは、わかりやすく、ユーザーが問題を解決するために役立つものである ##
優れたエラー メッセージとは、次のようなものです。
- 存在する: エラー メッセージに関する最大の問題は、存在しないことがよくあるということです。メッセージが必ず存在するようにします。 
- わかりやすい: 人間が判読できる、明瞭なエラー コード。
- 正確である: 何が問題であるかを正確に説明します。
- 建設的である: 問題を解決する方法を助言します。
- 礼儀正しい: ユーザーを非難したり、見下したりしません。エラーはリソース機能を直接実行したときに返されるものとは異なる可能性があるため、エンド ツー エンドのシナリオで (**Start-DscConfiguration** を使用して) エラーを確認します。 

## ログ メッセージは、わかりやすく、有益な情報が含まれている (-verbose、-debug、および ETW ログを含む) ##
リソースによって出力されるログがわかりやすく、ユーザーにとって価値のあるものであることを確認します。 リソースは、ユーザーに役立つ可能性のあるすべての情報を出力する必要がありますが、常にログが多い方がよいとは限りません。 冗長性および付加価値を提供しないデータを出力することは避ける必要があります。求めている情報を探して何百ものログ エントリを確認する必要がないようにします。 もちろん、ログを出力しないことはこの問題に対する適切な解決策ではありません。 

テストする場合は、詳細ログとデバッグ ログ (**Start-DscConfiguration** を -verbose および -debug スイッチを適切に指定して実行する)、および ETW ログも分析する必要があります。 DSC ETW ログを確認するには、イベント ビューアーに移動し、次のフォルダーを開きます。Applications and Services- Microsoft - Windows - Desired State Configuration。  既定では稼動チャネルがありますが、構成を実行する前に分析チャネルとデバッグ チャネルを有効にします。 分析/デバッグ チャネルを有効にするには、次のスクリプトを実行できます。
```powershell
$statusEnabled = $true
# Use "Analytic" to enable Analytic channel
$eventLogFullName = "Microsoft-Windows-Dsc/Debug" 
$commandToExecute = "wevtutil set-log $eventLogFullName /e:$statusEnabled /q:$statusEnabled   "
$log = New-Object System.Diagnostics.Eventing.Reader.EventLogConfiguration $eventLogFullName
if($statusEnabled -eq $log.IsEnabled)
{
    Write-Host -Verbose "The channel event log is already enabled"
    return
}     
Invoke-Expression $commandToExecute 
```
## リソースの実装にハードコードされたパスが含まれていない ##
リソースの実装にハードコードされたパスがないことを確認します (特に、それらが言語 (en-us) を想定する場合、または使用できるシステム変数がある場合)。
リソースが特定のパスにアクセスする必要がある場合、パスは他のコンピューターでは異なる可能性があるため、パスをハードコーディングする代わりに環境変数を使用します。

次に例を示します。

次の代わりに、
```
$tempPath = "C:\Users\kkaczma\AppData\Local\Temp\MyResource"
$programFilesPath = "C:\Program Files (x86)"
 ```
次のように記述できます。
```
$tempPath = Join-Path $env:temp "MyResource"
$programFilesPath = ${env:ProgramFiles(x86)} 
```
## リソースの実装にユーザー情報が含まれていない ##
コード内に電子メール名、アカウント情報、または、ユーザーの名前がないことを確認します。
## リソースが有効/無効な資格情報を使用してテストされている ##
リソースが資格情報をパラメーターとして受け取る場合:
- ローカル システム (またはリモート リソースのコンピューター アカウント) にアクセスがない場合に、リソースが動作することを確認します。
- Get、Set、および Test に指定された資格情報でリソースが動作することを検証します。 
- リソースが共有にアクセスする場合は、サポートする必要がある次のようなすべてのバリエーションをテストします。
  - 標準の Windows 共有。
  - DFS 共有。
  - SAMBA 共有 (Linux をサポートする場合)。

## リソースには対話型の入力は不要です。 ##
**Get/Set/Test-TargetResource** 関数は自動的に実行される必要があり、実行のいずれの段階でもユーザーの入力を待機することはできません (たとえば、これらの関数内で **Get-Credential** を使用することはできません)。 ユーザーの入力を提供する必要がある場合は、コンパイル フェーズ中にパラメーターとして構成に渡す必要があります。 
## リソース機能が十分にテストされている ##
このチェックリストには、テストする必要がある重要な項目や見落とされがちな項目が含まれています。 一連のテスト (テストするリソースに固有であり、ここに記載されていない、主に機能のテスト) があります。 負のテスト ケースを忘れないでください。 
## ベスト プラクティス: リソース モジュールに、ResourceDesignerTests.ps1 スクリプトを含む Test フォルダーが含まれている ##
リソース モジュール内に "Test" フォルダーを作成し、ResourceDesignerTests.ps1 ファイルを作成し、指定したモジュール内のすべてのリソースに対して **Test-xDscResource** と **Test-xDscSchema** を使用してテストを追加することをお勧めします。 この方法で、指定したモジュールのすべてのリソースのスキーマをすばやく検証し、発行する前にサニティ チェックを実行できます。
xRemoteFile の場合、ResourceTests.ps1 は次のように単純になります。
```powershell
Test-xDscResource ..\DSCResources\MSFT_xRemoteFile
Test-xDscSchema ..\DSCResources\MSFT_xRemoteFile\MSFT_xRemoteFile.schema.mof 
```
##ベスト プラクティス: リソース フォルダーにスキーマを生成するためのリソース デザイナー スクリプトが含まれている##
各リソースに、リソースの mof スキーマを生成する、リソース デザイナー スクリプトを含める必要があります。 このファイルは、<ResourceName>\ResourceDesignerScripts に配置し、Generate<ResourceName>Schema.ps1 という名前を付ける必要があります。xRemoteFile リソースの場合、このファイルの名前は GenerateXRemoteFileSchema.ps1 となり、次の内容が含まれます。
```powershell 
$DestinationPath = New-xDscResourceProperty -Name DestinationPath -Type String -Attribute Key -Description 'Path under which downloaded or copied file should be accessible after operation.'
$Uri = New-xDscResourceProperty -Name Uri -Type String -Attribute Required -Description 'Uri of a file which should be copied or downloaded. This parameter supports HTTP and HTTPS values.'
$Headers = New-xDscResourceProperty -Name Headers -Type Hashtable[] -Attribute Write -Description 'Headers of the web request.'
$UserAgent = New-xDscResourceProperty -Name UserAgent -Type String -Attribute Write -Description 'User agent for the web request.' 
$Ensure = New-xDscResourceProperty -Name Ensure -Type String -Attribute Read -ValidateSet "Present", "Absent" -Description 'Says whether DestinationPath exists on the machine'
$Credential = New-xDscResourceProperty -Name Credential -Type PSCredential -Attribute Write -Description 'Specifies a user account that has permission to send the request.'
$CertificateThumbprint = New-xDscResourceProperty -Name CertificateThumbprint -Type String -Attribute Write -Description 'Digital public key certificate that is used to send the request.'

New-xDscResource -Name MSFT_xRemoteFile -Property @($DestinationPath, $Uri, $Headers, $UserAgent, $Ensure, $Credential, $CertificateThumbprint) -ModuleName xPSDesiredStateConfiguration2 -FriendlyName xRemoteFile 
```
## ベスト プラクティス: リソースによる -whatif のサポート ##
リソースが "危険な" 操作を実行する場合は、-whatif 機能を実装することをお勧めします。 完了したら、whatif スイッチを使用しないでコマンドが実行された場合にどのようなことが発生するかについて、whatif 出力で正しく記述されていることを確認します。
また、-whatif スイッチが存在する場合は、その操作が実行されない (ノードの状態の変更は行われない) ことも確認します。 たとえば、File リソースをテストすると仮定します。 "test" の内容を持つファイル "test.txt" を作成する単純な構成を次に示します。
```powershell
configuration config
{
    node localhost 
    {
        File file
        {
            Contents="test"
            DestinationPath="C:\test\test.txt"
        }
    }
}
config 
```
-whatif スイッチを含む構成をコンパイルし、実行すると、構成を実行したときに発生することが出力に正確に示されます。 ただし、構成自体は実行されていません (test.txt ファイルは作成されていません)。
```powershell 
Start-DscConfiguration -path .\config -ComputerName localhost -wait -verbose -whatif
VERBOSE: Perform operation 'Invoke CimMethod' with following parameters, ''methodName' =
SendConfigurationApply,'className' = MSFT_DSCLocalConfigurationManager,'namespaceName' =
root/Microsoft/Windows/DesiredStateConfiguration'.
VERBOSE: An LCM method call arrived from computer CHARLESX1 with user sid
S-1-5-21-397955417-626881126-188441444-5179871.
What if: [X]: LCM:  [ Start  Set      ]
What if: [X]: LCM:  [ Start  Resource ]  [[File]file]
What if: [X]: LCM:  [ Start  Test     ]  [[File]file]
What if: [X]:                            [[File]file] The system cannot find the file specified.
What if: [X]:                            [[File]file] The related file/directory is: C:\test\test.txt.
What if: [X]: LCM:  [ End    Test     ]  [[File]file]  in 0.0270 seconds.
What if: [X]: LCM:  [ Start  Set      ]  [[File]file]
What if: [X]:                            [[File]file] The system cannot find the file specified.
What if: [X]:                            [[File]file] The related file/directory is: C:\test\test.txt.
What if: [X]:                            [C:\test\test.txt] Creating and writing contents and setting attributes.
What if: [X]: LCM:  [ End    Set      ]  [[File]file]  in 0.0180 seconds.
What if: [X]: LCM:  [ End    Resource ]  [[File]file]
What if: [X]: LCM:  [ End    Set      ]
VERBOSE: [X]: LCM:  [ End    Set      ]    in  0.1050 seconds.
VERBOSE: Operation 'Invoke CimMethod' complete.
```

この一覧ですべての内容を網羅しているわけではないですが、DSC リソースの設計、開発、テスト中に発生した多くの重要な問題を反映しています。



<!--HONumber=Oct16_HO1-->


