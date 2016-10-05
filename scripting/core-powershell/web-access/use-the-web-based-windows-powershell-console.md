---
title: "Web ベースの Windows PowerShell コンソールの使用"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 9c633d457db9d15621285b2662244c4190550f63

---

#  Web ベースの Windows PowerShell コンソールの使用

最終更新日 : 2013 年 6 月 24 日

適用対象: Windows Server 2012 R2、Windows Server 2012

Windows PowerShell® Web Access を使うと、Windows PowerShell® のユーザーは、Secure Sockets Layer (SSL) で保護された Web サイトにサインインして、Windows PowerShell のセッション、コマンドレット、およびスクリプトを使ってリモート コンピューターを管理できるようになります。 Windows PowerShell コンソールは Web ブラウザー内で実行されるため、携帯電話、タブレット コンピューター、公共のコンピューティング キオスク、ノート PC、共有や貸与されているコンピューターなど、さまざまなクライアント デバイスで表示することができます。 Web ベースの Windows PowerShell コンソールは、サインイン プロセスでユーザーが指定するリモート コンピューターを対象としています。 このトピックでは、Windows PowerShell Web Access の Web ベース コンソールにサインインして使用を開始する方法について説明します。

Windows PowerShell の使用方法や、コマンドレットまたはスクリプトの実行方法はこのトピックの対象外です。 Windows PowerShell およびスクリプト リソースの使用方法の詳細については、このトピックの最後にある「関連項目」セクションを参照してください。

このトピックの内容:

-   [サポートされているブラウザーとクライアント デバイス](#BKMK_browser)

-   [Windows PowerShell Web Access へのをサインイン](#BKMK_sign)

-   [サインアウトとタイムアウト](#BKMK_timeout)

-   [Web ベースの Windows PowerShell コンソールでの相違点](#BKMK_web)

<a href="" id="BKMK_browser"></a>
------------------------------------------------------------------------

Windows PowerShell Web Access は次のインターネット ブラウザーをサポートします。 モバイル ブラウザーは公式にはサポートされていませんが、それらの多くが Web ベースの Windows PowerShell コンソールを実行できるようです。 その他のブラウザーも、Cookie の許可、JavaScript の実行、および HTTPS Web サイトの実行に対応している場合は動作すると思われますが、公式にはテストされていません。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">サポートされているデスクトップ コンピューター ブラウザー</span></a>

------------------------------------------------------------------------

-   Windows® Internet Explorer® for Microsoft Windows® 8.0、9.0、10.0、および 11.0

-   Mozilla Firefox® 10.0.2

-   Google Chrome™ 17.0.963.56m for Windows

-   Apple Safari® 5.1.2 for Windows

-   Apple Safari 5.1.2 for Mac OS®

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">最低限でテスト済みのモバイル デバイスやブラウザー</span></a>

------------------------------------------------------------------------

-   Windows Phone 7 および 7.5

-   Google Android WebKit 3.1 Browser Android 2.2.1 (Kernel 2.6)

-   Apple Safari for iPhone operating system 5.0.1

-   Apple Safari for iPad 2 operating system 5.0.1

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">ブラウザーの要件</span></a>

------------------------------------------------------------------------

Web ベースの Windows PowerShell コンソールを使うには、ブラウザーが次の操作を実行できる必要があります。

-   Windows PowerShell Web Access ゲートウェイ Web サイトの Cookie を許可する。

-   HTTPS ページを開き、読み取る。

-   JavaScript を使う Web サイトを開き、実行する。

<a href="" id="BKMK_sign"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">Windows PowerShell Web Access へのをサインイン</span></a>
<a href="/en-us/library/hh831417(v=ws.11).aspx#Anchor_1" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access 管理者は、所属組織の Windows PowerShell Web Access ゲートウェイ Web サイトのアドレスを示す URL を担当者に提供する必要があります。 この Web サイトのアドレスは、既定で https://&lt;server_name&gt;/pswa となっています。 Windows PowerShell Web Access にサインインする前に、管理対象のリモート コンピューターの名前または IP アドレスがわかっていることを確認してください。 また担当者がリモート コンピューターの承認済みユーザーであることと、リモート コンピューターがリモート管理を許可するよう構成されている必要があります。 リモート管理を許可するようコンピューターを構成する方法の詳細については、[Windows PowerShell でのリモート コマンドの有効化および使用](https://technet.microsoft.com/magazine/ff700227.aspx)に関するページを参照してください。 コンピューターを構成してリモート管理を有効にする最も簡単な方法は、Windows PowerShell セッションを管理者特権で開き (**[管理者として実行]**)、コンピューター上で **Enable-PSRemoting -force** コマンドレットを実行することです。

### Windows PowerShell Web Access にサインインするには

1.  Windows PowerShell Web Access Web サイトをインターネット ブラウザーのウィンドウまたはタブで開きます。

2.  Windows PowerShell Web Access のサインイン ページで、ネットワーク ユーザー名、パスワード、および管理対象の (自分が承認済みユーザーとなっている) コンピューター名を入力します。 コンピューター名の代わりにカスタム サイトまたはプロキシ サーバーの URI を使うよう Windows PowerShell Web Access 管理者から指示されている場合は、**"接続の種類"** フィールドの **[接続 URI]** を選択し、URI を入力してください。

    <table>
    <colgroup>
    <col width="100%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th><span><img src="https://i-technet.sec.s-msft.com/dynimg/IC101471.jpeg" title="System_CAPS_note" alt="System_CAPS_note" id="s-e6f6a65cf14f462597b64ac058dbe1d0-system-media-system-caps-note" /></span><span class="alertTitle">注 </span></th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td><ul>
    <li><p>移行先コンピューターがワークグループ内にある場合は、ユーザー名を入力し、コンピューターにサインインする次の構文を使用します。&lt;<em>workgroup_name</em>&gt;\&lt;<em>user_name</em>&gt;します。</p></li>
    <li><p>対象のコンピューターがゲートウェイ サーバーの場合、<strong>[コンピューター名]</strong> フィールドで <strong>localhost</strong> を指定できます。</p></li>
    <li><p>対象のコンピューターがゲートウェイ サーバーとゲートウェイ サーバーがワークグループでは、使用できます <strong>localhost</strong> で、 <strong>コンピューター名</strong> フィールドには、localhost \ を使用しない&lt;<em>user_name</em>&gt; で、 <strong>ユーザー名</strong> フィールドです。 使用する必要があります &lt;<em>ワークグループ名</em>&gt;\&lt;<em>user_name</em>&gt;します。</p></li>
    </ul></td>
    </tr>
    </tbody>
    </table>

3.  **[オプションの接続設定]** セクションは、管理対象のリモート コンピューターの承認要件に関連しています。 オプションの接続設定に相当するパラメーターの詳細については、[Enter-PSSession コマンドレットのヘルプ](https://technet.microsoft.com/library/dd315384.aspx)を参照してください。

    通常、Windows PowerShell Web Access ゲートウェイの通過に使う資格情報は、管理対象のリモート コンピューターが認識する資格情報と同じです。 ただし、手順 2. で指定したリモート コンピューターを別の資格情報を使って管理する場合、**[オプションの接続設定]** セクションを展開して、別の資格情報を入力します。 それ以外の場合、手順 6. に進みます。

4.  Windows PowerShell Web Access 管理者が Windows PowerShell Web Access ユーザー用にカスタムのセッション構成を作成している場合、そのセッション構成の名前を **"構成名"** フィールドに入力します。 セッション構成の詳細については、Microsoft Web サイトの「[about_Session_Configurations](https://technet.microsoft.com/library/dd819508.aspx)」を参照してください。

5.  **[認証の種類]** の設定は、それ以外にするよう Windows PowerShell Web Access 管理者から指示された場合を除き、**[既定]** が設定されたままにしておきます。

6.  **[サインイン]** をクリックします。

<a href="" id="BKMK_timeout"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">サインアウトとタイムアウト</span></a>
<a href="/en-us/library/hh831417(v=ws.11).aspx#Anchor_2" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

次のいずれかの操作を実行すると、Web ベースの Windows PowerShell セッションからサインアウトします。

-   コンソールの右下隅にある **[サインアウト]** をクリックする (Windows Server 2012 のみ)

-   コンソールの右下隅にある **[保存]** または **[終了]** をクリックする (Windows Server 2012 R2 のみ)。 **[保存]** をクリックすると Windows PowerShell Web Access セッションが保存されて閉じます。後でセッションに再接続できます。 Windows PowerShell Web Access に再びサインインすると、保存したセッションの一覧が Windows PowerShell Web Access に表示されます。保存されているセッションを選択して再接続することも、新しいセッションを開始することもできます。 ユーザーが開くことのできるセッションの最大数 (保存されているものとアクティブの両方) は、ゲートウェイの管理者が構成します。

    **[終了]** をクリックすると、Windows PowerShell Web Access セッションが保存されずにサインアウトします。

-   同じブラウザー セッションまたは同じブラウザー セッションの新しいタブで、別のリモート コンピューターを管理するためにサインインする。 (これは、ゲートウェイ サーバーが Windows Server 2012 R2 を実行している場合は適用されません。Windows Server 2012 R2 で実行している Windows PowerShell Web Access では、同じブラウザー セッションの新しいタブで複数のユーザー セッションを開くことができます)。同じコンピューターで複数のアクティブ セッションを使う方法の詳細については、このトピックの「[Web ベース コンソールの制限事項](#BKMK_limits)」セクションの「複数の対象コンピューターへの同時接続」を参照してください。

-   セッションの非アクティブな状態が 20 分続いた場合。 ゲートウェイの管理者は、非アクティブ状態によるタイムアウトの長さをカスタマイズできます。詳細については、「[セッションの管理](https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx#BKMK_sesmgmt)」を参照してください。

    -   ユーザー自身がセッションを閉じたためではなく、ネットワーク エラーまたはその他の予期しないシャットダウンや障害のために、ユーザーがセッションから切断された場合、クライアント側でタイムアウト期間が経過するまで Windows PowerShell Web Access セッションは実行を続け、ターゲット コンピューターに接続されたままになります。 既定ではこのタイムアウト期間は 20 分であり、ゲートウェイ管理者が構成します。 既定の 20 分またはゲートウェイ管理者が指定したタイムアウト期間のどちらか短い方が経過すると、セッションは切断されます。

        ゲートウェイ サーバーが Windows Server 2012 R2 を実行している場合の Windows PowerShell Web Access では、ユーザーは保存されているセッションに再接続できますが、ゲートウェイ管理者によって指定されたタイムアウト期間が経過するまでは、保存されているセッションを表示または再接続できません。

-   ブラウザー ウィンドウまたはタブを閉じる。

-   ブラウザーが実行されているクライアント デバイスの電源を切る、またはネットワークから切断する。

-   Web コンソールで **[終了]** コマンドを実行する。 接続先のセッション構成が [NoLanguage](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pslanguagemode.aspx) モードをサポートするよう構成されている場合、または制限付き実行空間が指定されている場合は、このコマンドは機能しません。

もう一度サインインする場合は、Windows PowerShell Web Access の Web ページをもう一度開き、このトピックの「[Windows PowerShell Web Access にサインインするには](#BKMK_signin)」の手順に従ってサインインします。

<a href="" id="BKMK_web"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">Web ベースの Windows PowerShell コンソールでの相違点</span></a>
<a href="/en-us/library/hh831417(v=ws.11).aspx#Anchor_3" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access にサインインすると、Web ベースの Windows PowerShell コンソールがブラウザー ウィンドウまたはタブに表示されます。 コンソールはサインイン プロセスで指定したリモート コンピューターに接続されているため、このコンソールで使える Windows PowerShell コマンドレットまたはスクリプトは、対象のリモート コンピューターで利用可能なものだけです。 このセクションでは、Windows PowerShell Web Access コンソールにおけるその他の制限事項と、Windows PowerShell Web Access コンソールとインストール型の **PowerShell.exe** コンソールとの間の違いについて説明します。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">PowerShell.exe と機能面の相違点</span></a>

------------------------------------------------------------------------

Windows PowerShell のホスト機能の大部分は Windows PowerShell Web Access の Web ベース コンソールでも利用できますが、いくつかの機能は利用できません。

-   <span class="label">入れ子になった進行状況が表示されます。</span>  進行状況を報告するコマンドレットを使用すると、Windows PowerShell Web Access が表示する進行状況 GUI には、最上位レベルの情報だけが表示されます。

-   <span class="label">色の変更を入力します。</span>  入力色 (前景と背景) は変更できません。 出力、警告、詳細、およびエラー メッセージのスタイルは、スクリプトを実行することで変更できます。

-   <span class="label">PSHostRawUserInterface。</span>  Windows PowerShell Web Accessは Windows PowerShell リモート管理経由で実装され、リモートの実行空間を使います。 たとえば Windows コンソールへの書き込みを実行するあらゆるコマンドなど、一部のメソッドは Windows PowerShell Web Access によってこのインターフェイスに実装されません。 **PowerTab** などのコマンドは、Windows PowerShell Web Access では動作しません。

-   <span class="label">ファンクション キー。</span>  一部のファンクション キーは Windows PowerShell Web Access によってサポートされません。多くの場合、それらのコマンドがブラウザーによって予約されていることが原因です。

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th><p>サポートされないファンクション キー</p></th>
<th><p>操作</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Ctrl+C</p></td>
<td><p>Windows PowerShell Web Access では、<strong>Ctrl + C</strong> はブラウザーによるコンテンツのコピーに使われます。 コンソールが提供する <strong>[キャンセル]</strong> ボタンのほか、ユーザーは <strong>Ctrl + Q</strong> を使ってコマンドをキャンセルできます。</p></td>
</tr>
<tr class="even">
<td><p>Alt + Space、e、l</p></td>
<td><p>画面バッファーをスクロール</p></td>
</tr>
<tr class="odd">
<td><p>Alt + Space、e、f</p></td>
<td><p>画面バッファー内のテキストを検索</p></td>
</tr>
<tr class="even">
<td><p>Alt + Space、e、k</p></td>
<td><p>Select text to be copied from the screen bufferコピーするテキストを画面バッファーから選択</p></td>
</tr>
<tr class="odd">
<td><p>Alt + Space、e、p</p></td>
<td><p>クリップボードのコンテンツを Windows PowerShell コンソールに貼り付け</p></td>
</tr>
<tr class="even">
<td><p>Alt + Space、c</p></td>
<td><p>Windows PowerShell コンソールを閉じる</p></td>
</tr>
<tr class="odd">
<td><p>Ctrl + Break</p></td>
<td><p>Windows PowerShell ウィンドウを強制的に閉じる</p></td>
</tr>
<tr class="even">
<td><p>Ctrl + Home</p></td>
<td><p>現在のコマンド ラインの先頭から削除</p></td>
</tr>
<tr class="odd">
<td><p>Ctrl + End</p></td>
<td><p>コマンドラインの末尾まで削除</p></td>
</tr>
<tr class="even">
<td><p>F1</p></td>
<td><p>コマンド ライン上でカーソルを 1 文字分移動</p></td>
</tr>
<tr class="odd">
<td><p>F2</p></td>
<td><p>最後のコマンドをコピーし、入力する文字に合わせて新しいコマンドを作成</p></td>
</tr>
<tr class="even">
<td><p>F3</p></td>
<td><p>最後のコマンド ラインの内容を使ってコマンド ラインを完成させる</p></td>
</tr>
<tr class="odd">
<td><p>F4</p></td>
<td><p>カーソル位置から文字を削除</p></td>
</tr>
<tr class="even">
<td><p>F5</p></td>
<td><p>コマンド履歴をさかのぼってスキャン Windows PowerShell Web Access のコマンド履歴にあるコマンドにアクセスするには、Web ベース コンソールで <strong>[履歴]</strong> スクロール ボタンをクリックします。</p></td>
</tr>
<tr class="odd">
<td><p>F7</p></td>
<td><p>コマンド履歴のコマンドを対話的に選択</p></td>
</tr>
<tr class="even">
<td><p>F8</p></td>
<td><p>現在のテキストに一致するコマンドが表示された履歴をスキャン</p></td>
</tr>
<tr class="odd">
<td><p>F9</p></td>
<td><p>履歴から特定の番号の付いたコマンドを実行</p></td>
</tr>
<tr class="even">
<td><p>PageUp</p></td>
<td><p>履歴内の最初のコマンドを実行</p></td>
</tr>
<tr class="odd">
<td><p>PageDown</p></td>
<td><p>履歴内の最後のコマンドを実行</p></td>
</tr>
<tr class="even">
<td><p>Alt + F7</p></td>
<td><p>コマンド履歴の一覧を消去</p></td>
</tr>
</tbody>
</table>

<a href="" id="BKMK_limits"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">Web ベース コンソールの制限事項</span></a>

------------------------------------------------------------------------

-   <span class="label">ダブル ホップです。</span>   Windows PowerShell Web Access を使って新しいセッションを作成または操作しようとすると、ダブルホップ (最初の接続から 2 つ目のコンピューターに接続すること) の制限が発生します。 Windows PowerShell Web Access はリモートの実行空間を使いますが、現時点では、リモートの実行空間から 2 つ目のコンピューターへのリモート接続の確立を **PowerShell.exe** がサポートしていません。 たとえば、**Enter-PSSession** コマンドレットを使って既存の接続から 2 つ目のリモート コンピューターに接続しようとすると、"ネットワーク リソースを取得できません" など、さまざまなエラーが発生します。

    ダブルホップのエラーを避けるには、管理者が組織のネットワーク環境に CredSSP 認証を構成する必要があります。 CredSSP 認証の構成方法の詳細については、Microsoft Web サイトの [CredSSP による次ホップのリモート処理](http://blogs.msdn.com/b/powershell/archive/2008/06/05/credssp-for-second-hop-remoting-part-i-domain-account.aspx)に関するページを参照してください。 2 つ目のリモート コンピューターを管理する場合、明示的な資格情報を提供することも可能です。暗黙的な資格情報の場合、次ホップが許可される可能性が低くなります。

-   Windows PowerShell Web Access に使用され適用される制限事項は、リモートの Windows PowerShell セッションと同じです。 コンソールベースのエディターやテキストベースのメニュー プログラムなど、Windows コンソール API を直接呼び出すコマンドは動作しません。これは、これらのコマンドが、標準入力、出力、エラーの各パイプの読み取りと書き込みを実行しないためです。 このため、実行可能ファイルを起動するコマンド (**notepad.exe** など) や GUI を表示するコマンド (<span class="code">OpenGridView</span> や <span class="code">ogv</span> など) は動作しません。 この動作は担当者のエクスペリエンスに影響を与えます。つまり、担当者には Windows PowerShell Web Access がコマンドに応答していないように見えます。

-   タブ補完は、制限付き実行空間が指定されたセッション構成、または **NoLanguage** モードのセッション構成では動作しません。 管理者がタブ補完をサポートするようセッションを構成できますが、承認されていないユーザーに次の情報が公開される可能性があるというセキュリティ上の理由から推奨されません。

    -   内部ファイル システムのパス

    -   内部コンピューターの共有フォルダー

    -   実行空間の変数

    -   読み込まれる型または .NET Framework の名前空間

    -   環境変数

-   **NoLanguage** の Windows PowerShell Web Access セッション構成または制限付き実行空間にサインインしているユーザーは **[終了]** コマンドを実行してセッションを終了できません。 サインアウトするには、コンソール ページの **[サインアウト]** をクリックする必要があります。

-   <span class="label">複数のターゲット コンピューターへの同時接続します。</span>   ゲートウェイ サーバーが Windows Server 2012 を実行している場合、Windows PowerShell Web Access では、ブラウザー セッションごとに接続できるリモート コンピューターは 1 つだけです。一度サインインした後に別のブラウザー タブを使って複数のリモート コンピューターに接続することはできません。 新しいタブまたはブラウザー ウィンドウを開くと、現在のセッションの切断と新しいセッションの開始を確認するメッセージが Windows PowerShell Web Access により表示されます。このメッセージから新しい (または同じ) リモート コンピューターに接続できます。 ただし、異なるリモート コンピューターに対して個別のセッションを 2 つ以上実行する必要がある場合、Internet Explorer の機能を使って新しいセッションを作成できます。 Internet Explorer で新しいブラウザー セッションを開始するには、**Alt** キーを押して **[ファイル]** メニューを開き、**[新しいセッション]** をクリックします。 次に新しいセッションで Windows PowerShell Web Access の Web サイトを開き、サインインして別のリモート コンピューターにアクセスします。

    Windows PowerShell Web Access ゲートウェイが Windows Server 2012 R2 で実行されている場合、ユーザーは異なるブラウザー タブでリモート コンピューターへの複数の接続を開くことができます。 Web ベースの Windows PowerShell コンソールを使用してリモート コンピューターへの複数の接続を開く必要がある場合は、ゲートウェイ サーバーがこの機能をサポートしているかどうかを Windows PowerShell Web Access ゲートウェイ管理者に確認してください。

-   <span class="label">永続的な Windows PowerShell セッション (再接続)。</span>   Windows PowerShell Web Access ゲートウェイがタイムアウトすると、ゲートウェイと対象コンピューターとのリモート接続は閉じられます。 これによって、現在処理中のすべてのコマンドレットとスクリプトが停止されます。 実行時間の長いタスクを実行している場合は、Windows PowerShell の **-Job** インフラストラクチャを使うことが推奨されます。これにより、ジョブを開始し、コンピューターから切断した後に再接続して、ジョブを永続的なものにすることができます。 **-Job** コマンドレットを使うもう 1 つのメリットは、Windows PowerShell Web Access を使ってジョブを開始し、サインアウトした後に、Windows PowerShell Web Access またはその他のホスト (Windows PowerShell® 統合スクリプティング環境 (ISE) など) を使って再接続できる点です。

-   <span class="label">コンソールのサイズを変更します。</span>   **PowerShell.exe** コンソール ウィンドウのサイズは次の 3 つの方法で変更できます。

    -   マウスを使ってコンソール ウィンドウをドラッグして調整する

    -   コンソール プロパティの GUI を使って高さと幅のプロパティを変更する

    -   コマンドレットを使ってコンソール ウィンドウの高さと幅を変更する

        Windows PowerShell Web Access のコンソール ウィンドウは、次のコマンドレットを使って構成できます。 次の例では、ユーザーが Windows PowerShell Web Access コンソールの幅を **20** に変更しています。

        [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_778d5e55-9195-4bd7-b313-d1fbca7876e4'); "Copy to clipboard.")

            $newSize = $Host.UI.RawUI.WindowSize
            $newSize.Width = $newSize.Width - 20

            $oldSize = $Host.UI.RawUI.WindowSize

            $Host.UI.RawUI.WindowSize = $newSize

        同じ方法でコンソールの高さを変更できます。

        コンソール ビューをカスタマイズするその他の例については、[Windows PowerShell チームのブログ](http://blogs.msdn.com/b/powershell/)を参照してください。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">関連項目</span></a>
<a href="/en-us/library/hh831417(v=ws.11).aspx#Anchor_4" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

スクリプト センター - Hey, Scripting Guy! のページ[Windows PowerShell コマンドレット リファレンス](https://technet.microsoft.com/library/ee407531(ws.10).aspx)
[Microsoft TechNet の Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx)
[TTechNet スクリプト センター リポジトリ](http://gallery.technet.microsoft.com/scriptcenter)
[スクリプト センター - Hey, Scripting Guy!](https://technet.microsoft.com/scriptcenter)
[Windows PowerShell チーム ブログ](http://blogs.msdn.com/b/powershell/)

<span>表示:</span> 保護されている継承

<span class="stdr-votetitle">このページに立ちましたか。</span>
はい いいえ

その他にご意見はありますか。

<span class="stdr-count"><span class="stdr-charcnt">1500</span> 残りの文字数</span> スキップする送信

<span class="stdr-thankyou">ありがとう！</span> <span class="stdr-appreciate">お客様のフィードバックを歓迎いたします。</span>

[プロファイルを管理します。](https://social.technet.microsoft.com/profile)

|

<a href="javascript:void(0)" id="SiteFeedbackLinkOpener"><span id="FeedbackButton" class="FeedbackButton clip20x21"> <img src="https://i-technet.sec.s-msft.com/Areas/Epx/Content/Images/ImageSprite.png?v=635975720914499532" alt="Site Feedback" id="feedBackImg" class="cl_footer_feedback_icon" /> </span> サイトのフィードバック</a> サイトのフィードバック

<a href="javascript:void(0)" id="SiteFeedbackLinkCloser">x</a>

お客様のご体験をお聞かせください。

ページはすばやく表示されましたか?

<span> Yes<span> </span></span> <span> No<span> </span></span>

ページのデザインは気に入りましたか?

<span> Yes<span> </span></span> <span> No<span> </span></span>

詳しくお聞かせください。

-   [Flash ニュースレター](https://technet.microsoft.com/cc543196.aspx)
-   |
-   [問い合わせ先](https://technet.microsoft.com/cc512759.aspx)
-   |
-   [プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)
-   |
-   [使用条件](https://technet.microsoft.com/cc300389.aspx)
-   |
-   [商標](https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/)
-   |

© 2016 Microsoft

© 2016 Microsoft

サードパーティのスクリプトやコード、サードパーティから本 Web サイトへのリンク、あるいは本サイトからサードパーティへのリンクは、マイクロソフトではなく、そのようなコードの所有者によってお客様にライセンス供与されています。 ASP.NET Ajax CDN の使用条件 - http://www.asp.net/ajaxlibrary/CDN.ashx
<img src="https://m.webtrends.com/dcsjwb9vb00000c932fd0rjc7_5p3t/njs.gif?dcsuri=/nojavascript&amp;WT.js=No" alt="DCSIMG" id="Img1" width="1" height="1" />




<!--HONumber=Oct16_HO1-->


