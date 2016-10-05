# アンインストール手順

## コマンド プロンプトを使用
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

## コントロール パネルを使用
1.  **[コントロール パネル]** を開きます。
2.  **[プログラム]** を開き、**[プログラムのアンインストール]** を開きます。
3.  **[インストールされた更新プログラムを表示]** をクリックします。
4.  インストールされた更新プログラムの一覧から **[Windows Management Framework 5.0]** を選択します。 これは *KB3134758*、*KB3134759*、または *KB3134760* に対応しています。 **[アンインストール]** をクリックします。


<!--HONumber=Oct16_HO1-->


