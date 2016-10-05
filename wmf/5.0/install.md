# インストール手順

お使いのオペレーティング システムとアーキテクチャに該当する適切なパッケージをダウンロードします。

| オペレーティング システム       | アーキテクチャ | パッケージ名              | 
|------------------------|--------------|---------------------------| 
| Windows Server 2012 R2 | x64      | [Win8.1AndW2K12R2 KB3134758-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717507) | 
| Windows Server 2012    | x64      | [W2K12 KB3134759-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717506) | 
| Windows Server 2008 R2 | x64      | [Win7AndW2K8R2 KB3134760-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717504) |
| Windows 8.1            | x64          | [Win8.1AndW2K12R2 KB3134758-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717507) |
| Windows 8.1            | x86          | [Win8.1 KB3134758-x86.msu](http://go.microsoft.com/fwlink/?LinkID=717963) |
| Windows 7 SP1          | x64          | [Win7AndW2K8R2 KB3134760-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717504) |
| Windows 7 SP1          | x86          | [Win7 KB3134760-x86.msu](http://go.microsoft.com/fwlink/?LinkID=717962) |


**Windows エクスプ ローラー (またはエクスプ ローラーで Windows Server 2012 R2 および Windows 8.1) から WMF 5.0 をインストールします。**

1. MSU ファイルをダウンロードしたフォルダーに移動します。

2. MSU をダブルクリックして実行します。

**コマンド プロンプトから WMF 5.0 をインストールします。** 

1. コンピューターのアーキテクチャに適切なパッケージをダウンロードした後、管理者特権 (管理者として実行) を使ってコマンド プロンプト ウィンドウを開きます。 Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 の Server Core インストール オプションで、既定で管理者特権でコマンド プロンプトが開きます。

2. WMF 5.0 のインストール パッケージをダウンロードまたはコピーしたフォルダーにディレクトリを変更します。

3. 次のいずれかのコマンドを実行します。
    - Windows Server 2012 R2 または Windows 8.1 x64 を実行しているコンピューターで、**Win8.1AndW2K12R2-KB3134758-x64.msu /quiet** を実行します。
    - Windows Server 2012 を実行しているコンピューターで、**W2K12-KB3134759-x64.msu /quiet** を実行します。
    - Windows Server 2008 R2 SP1 または Windows 7 SP1 x64 を実行しているコンピューターで、**Win7AndW2K8R2-KB3134760-x64.msu /quiet** を実行します。
    - Windows 8.1 x86 を実行しているコンピューターで、**Win8.1-KB3134758-x86.msu /quiet** を実行します。
    - Windows 7 SP1 x86 を実行しているコンピューターで、**Win7-KB3134760-x86.msu /quiet** を実行します。

**Windows Server 2008 SP1 および Windows 7 SP1 用の追加のインストールに関する注記:**

次の前提条件が満たされていることを確認します。
- 最新のサービス パックがインストールされていること。
- [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) がインストールされていること。

*WinRM の依存関係:* Windows PowerShell Desired State Configuration (DSC) は、WinRM に依存します。 WinRM は、Windows Server 2008 R2 および Windows 7 では既定で無効になっています。 WinRM を有効にするには、Windows PowerShell 管理者特権セッションで **Set-WSManQuickConfig** を実行します。




<!--HONumber=Oct16_HO1-->


