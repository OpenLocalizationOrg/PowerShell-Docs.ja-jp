---
title: "Windows PowerShell Web Access のアンインストール"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: d54139714552943901f565a8525bb478ed308f09

---

#  Windows PowerShell Web Access をアンインストールする

最終更新日 : 2013 年 6 月 24 日

適用対象: Windows Server 2012 R2、Windows Server 2012

Windows Server 2012 R2 または Windows Server 2012 のいずれかを実行しているゲートウェイ サーバーから Windows PowerShell Web Access Web サイトおよびアプリケーションを削除するには、このトピックの手順に従います。 開始する前に、Web サイトを削除することを Web ベース コンソールのユーザーに通知してください。

<a href="" id="BKMK_uninstall"></a>

------------------------------------------------------------------------

ゲートウェイ サーバーから Windows PowerShell Web Access をアンインストールする前に、<span class="code">Uninstall-PswaWebApplication</span> コマンドレットを実行して Web サイトおよび Windows PowerShell Web Access Web アプリケーションを削除するか、IIS マネージャーの手順「[IIS マネージャーを使って Windows PowerShell Web Access Web サイトおよびアプリケーションを削除するには](#BKMK_delsite)」を実行します。

Windows PowerShell Web Access の実行に必要なため、Windows PowerShell Web Access をアンインストールしても、自動インストールされた IIS その他の機能はアンインストールされません。 アンインストール プロセスでは、Windows PowerShell Web Access が依存する機能はそのまま残されるため、必要に応じて手動でアンインストールしてください。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">推奨される (簡易) のアンインストール</span></a>
<a href="/en-us/library/dn282396(v=ws.11).aspx#Anchor_1" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

このセクションの手順では、Windows PowerShell コマンドレットを使用して Windows PowerShell Web Access Web アプリケーションと Windows PowerShell Web Access 機能の両方をアンインストールできます。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 1: web アプリケーションを削除します。</span></a>

------------------------------------------------------------------------

独自のカスタム Web サイト名を指定した場合は、コマンドに <span class="code">WebsiteName</span> パラメーターを追加し、Web サイト名を指定します。 (既定のアプリケーション **pswa** ではなく) カスタム Web アプリケーションを使用している場合は、コマンドに <span class="code">WebApplicationName</span> パラメーターを追加し、Web アプリケーションの名前を指定します。

#### Uninstall-PswaWebApplication コマンドレットを使って Web サイトおよびアプリケーションを削除するには

1.  次のいずれかを実行して Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** をクリックします。

2.  「**Uninstall-PswaWebApplication**」と入力して **Enter** キーを押します。

3.  テスト証明書使用している場合、次の例のように <span class="code">DeleteTestCertificate</span> パラメーターをコマンドレットに追加します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_28147344-ab2f-49e7-b1c2-6dbe649d4366'); "Copy to clipboard.")

        Uninstall-PswaWebApplication -DeleteTestCertificate

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 2: Windows PowerShell Web Access をアンインストールします。</span></a>

------------------------------------------------------------------------

#### Windows PowerShell コマンドレットを使って Windows PowerShell Web Access をアンインストールするには

1.  次のいずれかを実行して、管理者特権を使って Windows PowerShell セッションを開きます。 セッションを既に開いている場合は、次の手順に進んでください。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

2.  次のように入力して **Enter** キーを押します。*computer_name* は、Windows PowerShell Web Access を削除するリモート サーバーです。 <span class="code">–Restart</span> パラメーターによって、対象サーバーが自動的に再起動されます (削除で必要な場合)。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_7b534520-f292-471f-89e3-a1079c03e369'); "Copy to clipboard.")

        Uninstall-WindowsFeature –Name WindowsPowerShellWebAccess -ComputerName <computer_name> -Restart

    オフライン VHD に役割や機能を削除するには、<span class="code">-ComputerName</span> パラメーターと <span class="code">-VHD</span> パラメーターの両方を追加する必要があります。 <span class="code">-ComputerName</span> パラメーターには VHD のマウント先のサーバー名が格納され、<span class="code">-VHD</span> パラメーターには指定したサーバー上の VHD ファイルへのパスが格納されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_5d8f91ee-b91a-4653-b7df-e745187fd72d'); "Copy to clipboard.")

        Uninstall-WindowsFeature –Name WindowsPowerShellWebAccess –VHD <path> -ComputerName <computer_name> -Restart

3.  削除が完了したら、Windows PowerShell Web Access が削除されていることを確認します。これには、サーバー マネージャーの **[すべてのサーバー]** ページを開き、機能を削除したサーバーを選択して、選択したサーバーのページで **[役割と機能]** タイルを表示します。 また、選択したサーバーを対象として <span class="code">Get-WindowsFeature</span> コマンドレット (Get-WindowsFeature -ComputerName &lt;*computer_name*&gt;) を実行し、サーバーにインストールされている役割と機能の一覧を表示することもできます。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">カスタムのアンインストール</span></a>
<a href="/en-us/library/dn282396(v=ws.11).aspx#Anchor_2" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

このセクションの手順では、サーバー マネージャーの役割と機能の削除ウィザード 、および IIS マネージャー コンソールを使用して Windows PowerShell Web Access の Web アプリケーションと Windows PowerShell Web Access 機能の両方をアンインストールできます。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 1: web アプリケーションを削除します。</span></a>

------------------------------------------------------------------------

#### IIS マネージャーを使って Windows PowerShell Web Access Web サイトおよびアプリケーションを削除するには

1.  次のいずれかの方法で IIS マネージャー コンソールを開きます。 既に開いている場合は、次の手順に進んでください。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。 [サーバー マネージャー] の **[ツール]** メニューで **[インターネット インフォメーション サービス (IIS) マネージャー]** をクリックします。

    -   Windows の**スタート**画面で、**インターネット インフォメーション サービス (IIS) マネージャー**という名前の任意の一部を入力します。 **[アプリ]** の結果に表示されたショートカットをクリックします。

2.  IIS マネージャーのツリー ウィンドウで、Windows PowerShell Web Access Web アプリケーションを実行している Web サイトを選択します。

3.  **[操作]** ウィンドウの **[Web サイトの管理]** で **[停止]** をクリックします。

4.  ツリー ウィンドウで、Windows PowerShell Web Access Web アプリケーションを実行している Web サイトの Web アプリケーションを右クリックして、**[削除]** をクリックします。

5.  ツリー ウィンドウで **[アプリケーション プール]** を選択し、Windows PowerShell Web Access アプリケーション プールのフォルダーを選択して、**[操作]** ウィンドウの **[停止]** をクリックし、コンテンツ ウィンドウの **[削除]** をクリックします。

6.  IIS マネージャーを閉じます。

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
    <td><p>アンインストールでは証明書は削除されません。 自己署名証明書を作成している場合、または使用したテスト証明書を削除する場合は、IIS マネージャーで証明書を削除できます。</p></td>
    </tr>
    </tbody>
    </table>

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 2: Windows PowerShell Web Access をアンインストールします。</span></a>

------------------------------------------------------------------------

#### 役割と機能の削除ウィザードを使って Windows PowerShell Web Access をアンインストールするには

1.  サーバー マネージャーを既に開いている場合は、次の手順に進んでください。 サーバー マネージャーをまだ開いていない場合は、次のいずれかの方法で開きます。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。

    -   Windows の**スタート**画面で、**[サーバー マネージャー]** をクリックします。

2.  **[管理]** メニューの **[役割と機能の削除]** をクリックします。

3.  **[対象サーバーの選択]** ページで、削除対象の機能を備えているサーバーまたはオフライン VHD を選択します。 オフライン VHD を選択するには、最初に VHD マウント先のサーバーを選択してから、VHD ファイルを選択します。 対象サーバーを選択したら、**[次へ]** をクリックします。

4.  もう一度 **[次へ]** をクリックして **[機能の削除]** ページにスキップします。

5.  **[Windows PowerShell Web Access]** のチェック ボックスをすべてオフにして、**[次へ]** をクリックします。

6.  **[削除オプションの確認]** ページで、**[削除]** をクリックします。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">関連項目</span></a>
<a href="/en-us/library/dn282396(v=ws.11).aspx#Anchor_3" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

[Windows PowerShell Web Access のインストールと使用](https://technet.microsoft.com/en-us/library/hh831611(v=ws.11).aspx)
[IIS マネージャー ヘルプ](https://technet.microsoft.com/library/cc732664.aspx)

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


