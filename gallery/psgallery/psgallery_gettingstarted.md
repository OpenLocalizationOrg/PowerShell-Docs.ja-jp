# PowerShell のギャラリーを使ってみる

## PowerShell ギャラリーとは何ですか。

PowerShell ギャラリーは、PowerShell コンテンツの中央リポジトリです。
その中には、PowerShell コマンドおよび Desired State Configuration (DSC) のリソースを含む便利な PowerShell モジュールがあります。 PowerShell スクリプトを PowerShell ワークフローと一連のタスクの概要を説明するが含まれ、これらのタスク シーケンスを提供するものが検索することもできます。
、マイクロソフトによって作成された一部の項目と、他のユーザーは、PowerShell コミュニティによって作成します。

## 要件

PowerShell Gallery から、システムへの項目をダウンロードする必要があります、 [PowerShellGet](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) モジュールです。 次のいずれかで PowerShellGet モジュールを見つけることができます。 PowerShell Gallery からアイテムをダウンロードにサインインする必要はありません。

-   [Windows 10](http://go.microsoft.com/fwlink/?LinkID=624830&clcid=0x409)
-   [Windows Management Framework 5.0](http://go.microsoft.com/fwlink/?LinkId=398175)
-   [MSI インストーラー (用 PowerShell 3 および 4)](http://go.microsoft.com/fwlink/?LinkID=746217&clcid=0x409)

必要もあります PowerShellGet、 [NuGet プロバイダー](http://go.microsoft.com/fwlink/?LinkId=722208) PowerShell ギャラリーを使用します。 NuGet プロバイダーが、次の場所のいずれかにない場合は、PowerShellGet の初回使用時に自動的に NuGet プロバイダーをインストールするように促されます。

-   $env:path: ProgramFiles\\PackageManagement\\ProviderAssemblies
-   $env:path: LOCALAPPDATA\\PackageManagement\\ProviderAssemblies

また、実行する **インストール PackageProvider-名前 NuGet-Force** ダウンロードと NuGet のプロバイダーのインストールを自動化します。

  
NuGet の 2.8.5.201 より前のバージョンがあればをインストールし、NuGet の最新バージョンに切り替えるには、次の PowerShell コマンドレットを呼び出す必要があります。

1.  インストール PackageProvider NuGet MinimumVersion '2.8.5.201' の強制
2.  インポート PackageProvider NuGet MinimumVersion '2.8.5.201' の強制
3.  上記のインストール場所から NuGet の古いバージョンを削除します。

詳細については、次を参照してください。 <http://oneget.org/> します。

  
注: パッケージの形式が変更されたのためお勧め最近更新された項目をインストールするには、PowerShellGet とまたの最新バージョンに更新します。 Windows 10 は、に関する詳細 PowerShellGet が含まれている [ここ](http://go.microsoft.com/fwlink/?LinkID=624830&clcid=0x409)します。
PowerShellGet もダウンロードする一部の Windows 管理フレームワーク (WMF) 5.0 では、 [ここ](http://go.microsoft.com/fwlink/?LinkId=398175)します。

## PowerShell Gallery から項目を検出します。

PowerShell ギャラリーで項目を検索するにを使用して、 **Search** この web サイトにするか、モジュールおよびスクリプトのページを閲覧して制御します。 実行して PowerShell ギャラリーから項目を検索することもできます、 [**検索モジュール**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) と [**検索スクリプト**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) と項目の種類に応じてコマンドレット **-リポジトリ PSGallery**します。

次のパラメーターを使用して、[ギャラリーからの結果をフィルター処理を行う [検索モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) と [検索スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409)

- 名前
- AllVersions
- MinimumVersion
- RequiredVersion
- タグ
- 含まれています
- DscResource
- RoleCapability
- コマンド
- フィルター

ギャラリー内の特定の DSC リソースを探索して関心のみ場合は、実行、 [**検索 DscResource**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) コマンドレットです。
[**検索 DscResource**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) ギャラリーに含まれている DSC リソースにデータを返します。 まだ実行する必要がある DSC リソースは常に納品されるため、モジュールの一部として、 [インストール モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) をこれらの DSC リソースをインストールします。

## PowerShell Gallery 内の項目について学習してください。

関心がある項目を特定して、詳細情報を入手することもできます。 ギャラリーにその項目の特定のページを確認するには、これを行うことができます。 そのページには、すべてのアイテムと共にアップロードされるメタデータを表示することができます。 この項目のメタデータは、項目の作成者によって提供され、は Microsoft によって検証されません。 項目の所有者は、項目を公開するために使用するギャラリー アカウントに関連付けられた厳密、Author フィールドよりも信頼性。

思われる項目が、誠実に公開されていませんが判明した場合] をクリックして **不適切発言の報告** その項目のページにします。

実行している場合 [Find モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) または [Find スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409), 、返された PSGetModuleInfo オブジェクトでこのデータを表示することができます。 たとえばを実行している [**検索モジュールの名前 PSReadLine-リポジトリ PSGallery |Get-member**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) ギャラリーで PSReadLine モジュールのデータを返します。

## PowerShell Gallery から項目をダウンロードします。

PowerShell Gallery から項目をダウンロードするときに、次のプロセスをお勧めします。

### 検査

検査用のギャラリーから項目をダウンロードするには、いずれかを実行、 [**保存モジュール**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) または [**保存スクリプト**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) 項目の種類によって、コマンドレットです。 これにより、インストールすることがなく、項目をローカルに保存し、項目の内容を検査できます。 保存された項目を手動で削除してください。

、マイクロソフトによって作成された一部の項目と、他のユーザーは、PowerShell コミュニティによって作成します。 マイクロソフトでは、内容およびインストールする前に、このギャラリー項目のコードを確認することをお勧めします。

思われる項目が、誠実に公開されていませんが判明した場合] をクリックして **不適切発言の報告** その項目のページにします。

### ［インストール］

を使用するためのギャラリーから項目をインストールするには、いずれかを実行、 [**インストール モジュール**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) または [**インストール スクリプト**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) コマンドレットは、項目の種類によって異なります。

[インストール モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) $env:path にモジュールをインストール: 既定では ProgramFiles\\WindowsPowerShell\\Modules です。 これには、管理者アカウントが必要です。 追加する場合、 **-スコープ CurrentUser** $env:path をパラメーターで、モジュールがインストールされている: USERPROFILE\\Documents\\WindowsPowerShell\\Modules です。

[インストール スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) $env:path のため、スクリプトにインストールする: 既定では ProgramFiles\\WindowsPowerShell\\Scripts です。 これには、管理者アカウントが必要です。 追加する場合、 **-スコープ CurrentUser** $env:path するパラメーター、スクリプトがインストールされている: USERPROFILE\\Documents\\WindowsPowerShell\\Scripts です。

既定では、 [インストール モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) と [インストール スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) 項目の最新バージョンをインストールします。 項目の古いバージョンをインストールするには、追加、 **- RequiredVersion** パラメーター。

### 展開

Azure Automation に PowerShell ギャラリーから項目を展開する] をクリックして **Azure Automation への配置]** [項目の詳細] ページ。 Azure 管理ポータル、Azure アカウントの資格情報を使用してサインインする場所にリダイレクトされます。 依存しているアイテムの展開を展開するすべての依存関係を Azure Automation に注意してください。 追加することで、'Azure オートメーションを使用する展開'] ボタンを無効にすることができます、 **AzureAutomationNotSupported** 項目メタデータへのタグ。

Azure Automation の詳細についてを参照してください、 [Azure Automation の web サイト](http://azure.microsoft.com/en-us/services/automation/)します。

## PowerShell Gallery からアイテムの更新

PowerShell Gallery からインストールされている項目を更新するには、いずれかを実行、 [更新モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) または [更新スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) コマンドレットです。 その他のパラメーターなしで実行すると [更新モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) を実行して、インストールされている各モジュールを更新しようと [インストール モジュール](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409)します。
モジュールを選択して更新するには、追加、 **-名前** パラメーター。

同様に、追加のパラメーターがないときに実行 [更新スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) を実行して、インストールされている各スクリプトを更新しようとしても [インストール スクリプト](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409)します。
スクリプトを選択して更新するには、追加、 **-名前** パラメーター。

## PowerShell Gallery からインストールされているリスト項目

PowerShell Gallery からインストールしたモジュールを調べるには、実行、 [**Get InstalledModule**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) コマンドレットです。 このコマンドは、すべてのシステムにある PowerShell Gallery から直接インストールされたモジュールを一覧表示します。

同様に、PowerShell ギャラリーからインストールするスクリプトについては、実行、 [**Get InstalledScript**](http://go.microsoft.com/fwlink/?LinkID=760387&clcid=0x409) コマンドレットです。 このコマンドは、すべての PowerShell ギャラリーから直接インストールされたシステムにあるスクリプトを一覧表示します。


<!--HONumber=Oct16_HO1-->


