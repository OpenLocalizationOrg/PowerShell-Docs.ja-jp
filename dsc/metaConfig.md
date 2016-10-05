---
title: "ローカル構成マネージャーの構成"
ms.date: 2016-05-16
keywords: PowerShell, DSC
description: 
ms.topic: article
author: eslesar
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 5d37938869a71bea0d8a6349e680411b7d0200d9

---

# ローカル構成マネージャーの構成

> 適用先: Windows PowerShell 5.0

ローカル構成マネージャー (LCM) は、Windows PowerShell Desired State Configuration (DSC) のエンジンです。 LCM は、すべてのターゲット ノード上で実行され、ノードに送信される構成の解析と適用を担当します。 次のような DSC のその他のさまざまな側面も担当します。

* 更新モード (プッシュまたはプル) の決定。
* ノードで構成をプルし、適用する頻度の指定。
* ノードのプル サーバーへの関連付け。
* 部分構成の指定。

特殊な種類の構成を使用して、これらの各動作を指定するように LCM を構成します。 ここでは、LCM を構成する方法について説明します。

> **注**: このトピックは、Windows PowerShell 5.0 で導入された LCM に適用されます。 Windows PowerShell 4.0 での LCM の構成については、「[Windows PowerShell 4.0 Desired State Configuration のローカル構成マネージャー (LCM)](metaconfig4.md)」を参照してください。

## LCM 構成の作成と適用

LCM を構成するには、特殊な種類の構成を作成して実行します。 LCM 構成を指定するには、DscLocalConfigurationManager 属性を使用します。 LCM をプッシュ モードに設定する単純な構成を次に示します。

```powershell
[DSCLocalConfigurationManager()]
configuration LCMConfig
{
    Node localhost
    {
        Settings
        {
            RefreshMode = 'Push'
        }
    }
} 
```

標準構成と同様に、構成を呼び出し、実行して、構成 MOF を作成します (構成 MOF の作成については、「[構成のコンパイル](configurations.md#compiling-the-configuration)」を参照してください)。 通常の構成とは異なり、[Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットを呼び出すことによって LCM 構成を適用しません。 代わりに、[Set-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn521621.aspx) コマンドレットを呼び出して、パラメーターとして構成 MOF のパスを指定します。 構成を適用した後、[Get-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn407378.aspx) コマンドレットを呼び出して、LCM のプロパティを確認できます。

LCM 構成には、限定されたリソースのセットに対するブロックのみを含めることができます。 前の例では、呼び出されたリソースは、**Settings** のみです。 その他の使用可能なリソースは次のとおりです。

* **ConfigurationRepositoryWeb**: 構成の HTTP プル サーバーを指定します。 
* **ConfigurationRepositoryShare**: 構成の SMB プル サーバーを指定します。
* **ResourceRepositoryWeb**: モジュールの HTTP プル サーバーを指定します。
* **ResourceRepositoryShare**: モジュールの SMB プル サーバーを指定します。
* **ReportServerWeb**: レポートが送信される HTTP プル サーバーを指定します。
* **PartialConfiguration**: 部分構成を指定します。

## 基本設定

プル サーバーと部分構成の指定以外の LCM 構成のすべてのプロパティは、**Settings** ブロックで構成されます。 **Settings** ブロックでは、次のプロパティを使用できます。

|  プロパティ  |  種類  |  説明   | 
|----------- |------- |--------------- | 
| ConfigurationModeFrequencyMins| UInt32| 現在の構成がチェックおよび適用される頻度 (分単位) ConfigurationMode プロパティが ApplyOnly に設定されている場合、このプロパティは無視されます。 既定値は 15 です。 <br> __注__: このプロパティの値が __RefreshFrequencyMins__ プロパティの値の倍数であるか、または __RefreshFrequencyMins__ プロパティの値がこのプロパティの値の倍数であるか、そのいずれかである必要があります。| 
| RebootNodeIfNeeded| ブール| 再起動が必要な構成が適用された後にノードを自動的に再起動するには、これを __$true__ に設定します。 設定しない場合は、再起動が必要な構成のノードを手動で再起動する必要があります。 既定値は __$false__ です。| 
| ConfigurationMode| string | LCM が実際に構成をターゲット ノードに適用する方法を指定します。 指定できる値は __"ApplyOnly"__、__"ApplyandMonitior"(既定)__、__"ApplyandAutoCorrect"__ です。 <ul><li>__"ApplyOnly"__: DSC によって構成が適用され、その後何も行われません。ただし、ターゲット ノードに新しい構成がプッシュされたか、新しい構成がサーバーからプルされた場合を除きます。 新しい構成を最初に適用した後、DSC では以前に構成した状態からのずれを確認しません。 DSC は成功するまで構成の適用を試みて、成功すると __ApplyOnly__ が有効になります。 </li><li> __"ApplyAndMonitor"__: これは既定値です。 LCM は、新しい構成を適用します。 新しい構成を最初に適用した後、ターゲット ノードが望ましい状態からずれた場合、DSC では、ログで不一致を報告します。 DSC は成功するまで構成の適用を試みて、成功すると __ApplyAndMonitor__ が有効になります。</li><li>__ApplyAndAutoCorrect__: DSC によって新しい構成が適用されます。 新しい構成を最初に適用した後、ターゲット ノードが望ましい状態からずれた場合、DSC では、ログで不一致を報告し、現在の構成を再度適用します。</li></ul>| 
| ActionAfterReboot| string| 構成の適用中の再起動後の動作を指定します。 指定できる値は __"ContinueConfiguration(default)"__ と __"StopConfiguration"__ です。 <ul><li> __ContinueConfiguration__: コンピューターの再起動後、現在の構成を引き続き適用します。</li><li>__StopConfiguration__: コンピューターの再起動後、現在の構成の適用を停止します。</li></ul>| 
| RefreshMode| string| LCM が構成を取得する方法を指定します。 指定できる値は、__"Disabled"__、__"Push(既定)"__、__"Pull"__ です。 <ul><li>__"Disabled"__: このノードの DSC 構成が無効になります。</li><li> __"Push"__: [Start-DscConfiguration](https://technet.microsoft.com/en-us/library/dn521623.aspx) コマンドレットを呼び出すことによって構成を開始します。 構成は、ノードにすぐに適用されます。 これは、既定値です。</li><li>__Pull:__ プル サーバーから構成が定期的にチェックされるようにノードを構成します。 このプロパティが __Pull__ に設定されている場合は、__ConfigurationRepositoryWeb__ または __ConfigurationRepositoryShare__ ブロックでプル サーバーを指定する必要があります。 プル サーバーの詳細については、「[DSC Web プル サーバーのセットアップ](pullServer.md)」をご覧ください。</li></ul>| 
| CertificateID| string| 構成へのアクセスの資格情報をセキュリティ保護するために使用される証明書を指定する GUID。 詳細については、「[Want to secure credentials in Windows PowerShell Desired State Configuration? (Windows PowerShell Desired State Configuration で資格情報をセキュリティ保護する)](http://blogs.msdn.com/b/powershell/archive/2014/01/31/want-to-secure-credentials-in-windows-powershell-desired-state-configuration.aspx)」をご覧ください。| 
| ConfigurationID| string| プル モードでプル サーバーから取得する構成ファイルを識別する GUID。 構成 MOF の名前が ConfigurationID.mof の場合、ノードはプル サーバーで構成をプルします。<br> __注:__ このプロパティを設定すると、__RegistrationKey__ を使用したプル サーバーへのノードの登録は機能しません。 詳細については、「[構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md)」をご覧ください。| 
| RefreshFrequencyMins| Uint32| LCM がプル サーバーをチェックして、更新された構成を取得する時間間隔 (分)。 この値は、LCM がプル モードで構成されていない場合は無視されます。 既定値は 30 です。<br> __注:__ このプロパティの値が __ConfigurationModeFrequencyMins__ プロパティの値の倍数であるか、または __ConfigurationModeFrequencyMins__ プロパティの値がこのプロパティの値の倍数であるか、そのいずれかである必要があります。| 
| AllowModuleOverwrite| ブール| 構成サーバーからダウンロードされた新しい構成がターゲット ノードの古い構成を上書きできる場合は、__$TRUE__。 それ以外の場合は、$FALSE。| 
| DebugMode| string| 使用できる値は __None (既定)__、__ForceModuleImport__、および __All__ です。 <ul><li>キャッシュされたリソースを使用する場合は、__None__ に設定します。 これが既定値であり、運用シナリオではこの値を使う必要があります。</li><li>__ForceModuleImport__ に設定すると、以前に読み込まれ、キャッシュされた DSC リソース モジュールも LCM によって再読み込みされます。 これは、使用時に各モジュールが再読み込みされるため、DSC 操作のパフォーマンスに影響します。 通常、リソースのデバッグ中には、この値を使用します</li><li>このリリースでは、__All__ は、__ForceModuleImport__ と同じです。</li></ul> |
| ConfigurationDownloadManagers| CimInstance[]| 使われていません。 __ConfigurationRepositoryWeb__ ブロックと __ConfigurationRepositoryShare__ ブロックを使用して、構成プル サーバーを定義します。| 
| ResourceModuleManagers| CimInstance[]| 使われていません。 __ResourceRepositoryWeb__ ブロックと __ResourceRepositoryShare__ ブロックを使用して、リソース プル サーバーを定義します。| 
| ReportManagers| CimInstance[]| 使われていません。 __ReportServerWeb__ ブロックを使用して、レポート プル サーバーを定義します。| 
| PartialConfigurations| CimInstance| 実装されていません。 使用しないでください。| 
| StatusRetentionTimeInDays | UInt32| LCM が現在の構成の状態を保持する日数。| 

## プル サーバー

プル サーバーは、DSC ファイルの一元管理の場所として使用される OData Web サービスまたは SMB 共有のいずれかです。 LCM 構成では、次の種類のプル サーバーの定義がサポートされています。

* **構成サーバー**: DSC 構成のリポジトリ。 **ConfigurationRepositoryWeb** (Web ベースのサーバーの場合) ブロックと **ConfigurationRepositoryShare** (SMB ベースのサーバーの場合) ブロックを使用して、構成サーバーを定義します。
* リソース サーバー - PowerShell モジュールとしてパッケージ化された DSC リソースのリポジトリ。 **ResourceRepositoryWeb** (Web ベースのサーバーの場合) ブロックと **ResourceRepositoryShare** (SMB ベースのサーバーの場合) ブロックを使用して、リソース サーバーを定義します。
* レポート サーバー - DSC がレポート データを送信するサービス。 **ReportServerWeb** ブロックを使用して、レポート サーバーを定義します。 レポート サーバーは、Web サービスである必要があります。

プル サーバーのセットアップと使用については、「[DSC Web プル サーバーのセットアップ](pullServer.md)」をご覧ください。

## 構成サーバーのブロック

Web ベースの構成サーバーを定義するには、**ConfigurationRepositoryWeb** ブロックを作成します。 **ConfigurationRepositoryWeb** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---| 
|AllowUnsecureConnection|ブール|認証なしのノードからサーバーへの接続を許可するには、**$TRUE** に設定します。 認証を要求するには、**$FALSE** に設定します。|
|CertificateID|string|サーバーへの認証に使用する証明書を表す GUID。|
|ConfigurationNames|String[]|ターゲット ノードによってプルされる構成の名前の配列。 ノードが **RegistrationKey** を使用してプル サーバーに登録されている場合にのみ使用されます。 詳細については、「[構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md)」をご覧ください。|
|RegistrationKey|string|プル サーバーにノードを登録する GUID。 詳細については、「[構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md)」を参照してください。|
|ServerURL|string|構成サーバーの URL。|

SMB ベースの構成サーバーを定義するには、**ConfigurationRepositoryShare** ブロックを作成します。 **ConfigurationRepositoryShare** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---|
|Credential|MSFT_Credential|SMB 共有への認証に使用される資格情報。|
|SourcePath|string|SMB 共有のパス。|

## リソース サーバーのブロック

Web ベースのリソース サーバーを定義するには、**ResourceRepositoryWeb** ブロックを作成します。 **ResourceRepositoryWeb** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---|
|AllowUnsecureConnection|ブール|認証なしのノードからサーバーへの接続を許可するには、**$TRUE** に設定します。 認証を要求するには、**$FALSE** に設定します。|
|CertificateID|string|サーバーへの認証に使用する証明書を表す GUID。|
|RegistrationKey|string|プル サーバーにノードを指定する GUID。 詳細については、「How to register a node with a DSC pull server (DSC プル サーバーにノードを登録する方法)」を参照してください。|
|ServerURL|string|構成サーバーの URL。|
 
SMB ベースのリソース サーバーを定義するには、**ResourceRepositoryShare** ブロックを作成します。 **ResourceRepositoryShare** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---|
|Credential|MSFT_Credential|SMB 共有への認証に使用される資格情報。|
|SourcePath|string|SMB 共有のパス。|

## レポート サーバーのブロック

レポート サーバーは、OData Web サービスである必要があります。 レポート サーバーを定義するには、**ReportServerWeb** ブロックを作成します。 **ReportServerWeb** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---| 
|AllowUnsecureConnection|ブール|認証なしのノードからサーバーへの接続を許可するには、**$TRUE** に設定します。 認証を要求するには、**$FALSE** に設定します。|
|CertificateID|string|サーバーへの認証に使用する証明書を表す GUID。|
|RegistrationKey|string|プル サーバーにノードを指定する GUID。 詳細については、「How to register a node with a DSC pull server (DSC プル サーバーにノードを登録する方法)」を参照してください。|
|ServerURL|string|構成サーバーの URL。|

## 部分構成

部分構成を定義するには、**PartialConfiguration** ブロックを作成します。 部分構成の詳細については、「[PowerShell Desired State Configuration の部分構成](partialConfigs.md)」をご覧ください。 **PartialConfiguration** は次のプロパティを定義します。

|プロパティ|種類|説明|
|---|---|---| 
|ConfigurationSource|string[]|**ConfiguratoinRepositoryWeb** および **ConfigurationRepositoryShare** ブロックで以前に定義した、部分構成をプルする構成サーバーの名前の配列。|
|DependsOn|string{}|この部分構成が適用される前に完了する必要があるその他の構成の名前の一覧。|
|説明|string|部分構成を記述するために使用するテキスト。|
|ExclusiveResources|string[]|この部分構成に固有のリソースの配列。|
|RefreshMode|string|LCM がこの部分構成を取得する方法を指定します。 指定できる値は、__"Disabled"__、__"Push(既定)"__、__"Pull"__ です。 <ul><li>__Disabled__: この部分的な構成が無効になります。</li><li> __Push__: [Publish-DscConfiguration](https://technet.microsoft.com/en-us/library/mt517875.aspx) コマンドレットを呼び出すと、部分構成がノードにプッシュされます。 ノードのすべての部分構成がプッシュされたか、またはサーバーからプルされた後、`Start-DscConfiguration –UseExisting` を呼び出すことによって構成を開始できます。 これは、既定値です。</li><li>__Pull__: プル サーバーから部分構成が定期的にチェックされるようにノードを構成します。 このプロパティが __Pull__ に設定されている場合は、__ConfigurationSource__ プロパティでプル サーバーを指定する必要があります。 プル サーバーの詳細については、「[DSC Web プル サーバーのセットアップ](pullServer.md)」をご覧ください。</li></ul>|
|ResourceModuleSource|string[]|この部分構成に必要なリソースのダウンロード元となるリソース サーバーの名前の配列。 これらの名前は、**ResourceRepositoryWeb** および **ResourceRepositoryShare** ブロックで以前に定義したリソース サーバーを参照する必要があります。|

## 参照 

### 概念
[Windows PowerShell Desired State Configuration の概要](overview.md)
 
[DSC Web プル サーバーのセットアップ](pullServer.md) 

[Windows PowerShell 4.0 Desired State Configuration のローカル構成マネージャー (LCM)](metaConfig4.md) 

### その他のリソース
[Set-DscLocalConfigurationManager](https://technet.microsoft.com/en-us/library/dn521621.aspx) 

[構成名を使用したプル クライアントのセットアップ](pullClientConfigNames.md) 




<!--HONumber=Oct16_HO1-->


