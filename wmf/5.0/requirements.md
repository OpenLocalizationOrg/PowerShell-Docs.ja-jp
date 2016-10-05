# システム要件

- WMF 5.0 RTM をインストールする前に最新の Windows Update をインストールします。
- WMF 5.0 RTM は、次のオペレーティング システムにのみインストールすることができます。

    | オペレーティング システム       | エディション         | 前提条件        |  パッケージ リンク |
    |------------------------|--------------|------------------|----------------------| --------------|
    | Windows Server 2012 R2 |  |  | [Win8.1AndW2K12R2 KB3134758-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717507) |
    | Windows Server 2012    |  |  | [W2K12 KB3134759-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717506) |
    | Windows Server 2008 R2 SP1 | IA64 を除くすべて | [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) と [.NET Framework 4.5 以上](https://msdn.microsoft.com/en-us/library/5a4x27ek.aspx)がインストールされていること| [Win7AndW2K8R2 KB3134760-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717504)|
    | Windows 8.1 | Pro、Enterprise | | **x64:**  [Win8.1AndW2K12R2-KB3134758-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717507) </br> **x86:**  [Win8.1-KB3134758-x86.msu](http://go.microsoft.com/fwlink/?LinkID=717963)||
    | Windows 7 SP1 | すべて | [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) と [.NET Framework 4.5 以上](https://msdn.microsoft.com/en-us/library/5a4x27ek.aspx)がインストールされていること | **x64:**  [Win7AndW2K8R2-KB3134760-x64.msu](http://go.microsoft.com/fwlink/?LinkId=717504)  </br> **x86:**  [Win7-KB3134760-x86.msu](http://go.microsoft.com/fwlink/?LinkID=717962)|

# インストール手順

### Windows エクスプローラー (またはファイル エクスプローラー) から WMF 5.0 をインストールするには:

1. MSU ファイルをダウンロードしたフォルダーに移動します。

2. MSU をダブルクリックして実行します。

### コマンド プロンプトから WMF 5.0 をインストールするには:

1. コンピューターのアーキテクチャに適切なパッケージをダウンロードした後、管理者特権 (管理者として実行) を使ってコマンド プロンプト ウィンドウを開きます。 Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 の Server Core インストール オプションで、既定で管理者特権でコマンド プロンプトが開きます。

2. WMF 5.0 のインストール パッケージをダウンロードまたはコピーしたフォルダーにディレクトリを変更します。

3. 次のいずれかのコマンドを実行します。
    - Windows Server 2012 R2 または Windows 8.1 x64 を実行しているコンピューターで、**Win8.1AndW2K12R2-KB3134758-x64.msu /quiet** を実行します。
    - Windows Server 2012 を実行しているコンピューターで、**W2K12-KB3134759-x64.msu /quiet** を実行します。
    - Windows Server 2008 R2 SP1 または Windows 7 SP1 x64 を実行しているコンピューターで、**Win7AndW2K8R2-KB3134760-x64.msu /quiet** を実行します。
    - Windows 8.1 x86 を実行しているコンピューターで、**Win8.1-KB3134758-x86.msu /quiet** を実行します。
    - Windows 7 SP1 x86 を実行しているコンピューターで、**Win7-KB3134760-x86.msu /quiet** を実行します。

### Windows Server 2008 R2 SP1 および Windows 7 SP1 でのインストールに関する追加の注記:

次の前提条件が満たされていることを確認します。
- 最新のサービス パックがインストールされていること。
- [WMF 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855) がインストールされていること。
- [.NET framework 4.5 以上](https://msdn.microsoft.com/en-us/library/5a4x27ek.aspx) がインストールされていること。

**WMF 4.0 の依存関係**

Windows Server 2008 R2 SP1 システムと Windows 7 SP1 システムには、組み込みの PowerShell 2.0、WinRM、および WMI があります。 これらの組み込みコンポーネントを更新する WMF 3.0 パッケージと WMF 4.0 パッケージは、Windows Server 2008 R2 SP1 および Windows 7 SP1 のリリース後にリリースされました。 WMF 3.0 パッケージと WMF 4.0 パッケージのインストール/アンインストールでは、次のアップグレード パスでいくつかの問題が見つかりました。

- 組み込み --> WMF 4.0
- 組み込み --> WMF 3.0 --> WMF4.0 

これらのすべての問題を、WMF 4.0 パッケージで修正しました。 したがって、Windows Server 2008 R2 SP1 および Windows 7 SP1 に WMF 5.0 をインストールするには、WMF 4.0 が前提条件になります。 WMF 5.0 にアップグレードする前に WMF 4.0 をインストールしなかった場合に発生する可能性のある問題を下に示します。

- Windows 7 SP1 および Windows Server 2008 R2 SP1 で、WMF 3.0 または WMF 5.0 (前提条件の WMF 4.0 がインストールされていない場合) をアンインストールすると、転送されたイベント ログが利用できなくなり、イベント ビューアーに EventCollector ログが表示されなくなります ([KB2809215](https://support.microsoft.com/en-us/kb/2809215))。
- **Windows 7 SP1 および Windows Server 2008 R2 SP1 で、組み込みの PowerShell 2.0 から直接 WMF 5.0 にアップグレードした場合 ([KB2872035](https://support.microsoft.com/en-us/kb/2872035))、または WMF 3.0 から WMF 5.0 にアップグレードした場合 ([KB2872047](https://support.microsoft.com/en-us/kb/2872047))、PSModulePath 環境変数に対するカスタマイズが既定値にリセットされます。

**WinRM の依存関係**

Windows PowerShell Desired State Configuration (DSC) は、WinRM に依存します。 WinRM は、Windows Server 2008 R2 SP1 および Windows 7 SP1 では既定で無効になっています。 WinRM を有効にするには、Windows PowerShell 管理者特権セッションで **Set-WSManQuickConfig** を実行します。

# アンインストール手順

### コマンド プロンプトを使用

1.  **コマンド プロンプト**を開きます。

2.  [Windows Update スタンドアロン ランチャー](https://support.microsoft.com/en-us/kb/934307)を次のように実行します。

Windows Server 2012 R2 および Windows 8.1:
```powershell
wusa /uninstall /kb:3134758
```
Windows Server 2012:
```powershell
wusa /uninstall /kb:3134759
```
Windows Server 2008 R2 SP1 および Windows 7 SP1:
```powershell
wusa /uninstall /kb:3134760
```

### コントロール パネルを使用

1.  **[コントロール パネル]** を開きます。

2.  **[プログラム]** を開き、**[プログラムのアンインストール]** を開きます。

3.  **[インストールされた更新プログラムを表示]** をクリックします。

4.  インストールされた更新プログラムの一覧から **[Windows Management Framework 5.0]** を選択します。 これは *KB3134758*、*KB3134759*、または *KB3134760* に対応しています。 **[アンインストール]** をクリックします。


<!--HONumber=Oct16_HO1-->


