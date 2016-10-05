---
title: "Windows PowerShell Web Access のインストールと使用"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: 75e9b6525d74a8bc632b9fe2e64183717f59776f

---

#  Windows PowerShell Web Access のインストールと使用

最終更新日: 2013 年 11 月 5 日

適用対象: Windows Server 2012 R2、Windows Server 2012

Windows Server® 2012 で初めて導入された Windows PowerShell® Web Access は、Windows PowerShell ゲートウェイとして機能し、リモート コンピューターを対象とした Web ベースの Windows PowerShell コンソールを提供します。 これを使うと、IT 担当者は、クライアント デバイスで必要な Windows PowerShell、リモート管理ソフトウェア、またはブラウザー プラグインをインストールしなくても、Web ブラウザーの Windows PowerShell コンソールから Windows PowerShell コマンドおよびスクリプトを実行できます。 Web ベースの Windows PowerShell コンソールを実行するのに必要なのは、適切に構成された Windows PowerShell Web Access ゲートウェイと、JavaScript® をサポートし Cookie を有効にしたクライアント デバイス ブラウザーだけです。

クライアント デバイスの例としては、ノート PC、仕事用ではないパーソナル コンピューター、貸与されたコンピューター、タブレット コンピューター、Web キオスク、Windows ベース以外のオペレーティング システムを実行しているコンピューター、および携帯電話のブラウザーなどがあります。 IT 担当者は、インターネットと Web ブラウザーにアクセスできるデバイスを使って、リモートの Windows ベース サーバーの重要な管理タスクを実行できます。

ゲートウェイを適切にセットアップおよび構成した後は、Web ブラウザーを使って Windows PowerShell コンソールにアクセスできます。 セキュリティで保護された Windows PowerShell Web Access の Web サイトを開くと、認証成功後に Web ベースの Windows PowerShell コンソールを実行できます。

Windows PowerShell Web Access のセットアップおよび構成プロセスは、次の 3 つの手順で構成されます。

1.  Windows PowerShell Web Access のインストール

2.  ゲートウェイの構成

3.  Web ベースの Windows PowerShell コンソールへのアクセスをユーザーに許可する承認規則の構成

Windows PowerShell Web Access をインストールして構成する前に、このガイドをすべて読むことをお勧めします。このガイドでは、Windows PowerShell Web Access のインストール、セキュリティによる保護、およびアンインストールを行うための手順を説明しています。 「[Web ベースの Windows PowerShell コンソールの使用](https://technet.microsoft.com/en-us/library/hh831417(v=ws.11).aspx)」トピックでは、Web ベースのコンソールにサインインする方法と、Web ベースの Windows PowerShell コンソールと **powershell.exe** コンソールの制限と相違点について説明しています。 Web ベースのコンソールのエンド ユーザーは「[Web ベースの Windows PowerShell コンソールの使用](https://technet.microsoft.com/en-us/library/hh831417(v=ws.11).aspx)」を読んでください。このガイドをこれ以上読む必要はありません。

このトピックは Web サーバー (IIS) の操作に関する詳しいガイドを提供するものではありません。このトピックでは、Windows PowerShell Web Access ゲートウェイの構成に必要な手順だけを説明します。 IIS での Web サイトの構成とセキュリティ保護については、「関連項目」セクションの IIS に関するドキュメント リソースを参照してください。

次の図は、Windows PowerShell Web Access のしくみを示しています。

<span><img src="https://i-technet.sec.s-msft.com/dynimg/IC564303.jpeg" title="Windows PowerShell Web Access diagram" alt="Windows PowerShell Web Access diagram" id="ee15fa8f-ce13-49e5-933d-514f6d60a2b1" /></span>

このトピックの内容:

-   [Windows PowerShell Web Access を実行するための要件](#BKMK_reqs)

-   [ブラウザーとクライアント デバイスのサポート](#BKMK_browser)

-   [推奨される (簡易) の展開](#BKMK_recm)

-   [カスタムの配置](#BKMK_custom)

-   [正規の証明書を構成します。](#BKMK_configcert)

<a href="" id="BKMK_reqs"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">Windows PowerShell Web Access を実行するための要件</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_0" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access を使用するには、ゲートウェイを実行するサーバーで Web サーバー (IIS)、.NET Framework 4.5、Windows PowerShell 3.0 または Windows PowerShell 4.0 が実行されている必要があります。 Windows PowerShell Web Access は、サーバー マネージャーの役割および機能の追加ウィザードを使用するか、サーバー マネージャーのWindows PowerShell のデプロイ コマンドレットを使用して、Windows Server 2012 R2 または Windows Server 2012 を実行中のサーバーにインストールできます。 サーバー マネージャーまたはデプロイ コマンドレットを使って Windows PowerShell Web Access をインストールすると、インストール プロセスの一環として、必要な役割と機能が自動的に追加されます。

Windows PowerShell Web Access により、リモート ユーザーは Web ブラウザーで Windows PowerShell を使って、組織内のコンピューターにアクセスできます。 Windows PowerShell Web Access は便利で強力な管理ツールですが、Web ベースのアクセスを利用するとセキュリティ上のリスクが生じるため、できる限りセキュリティを確保して構成する必要があります。 Windows PowerShell Web Access ゲートウェイを構成する管理者は、利用可能なセキュリティ層を使うことをお勧めします。このセキュリティ層とは、Windows PowerShell Web Access に含まれるコマンドレット ベースの承認規則と、Web サーバー (IIS) およびサード パーティ アプリケーションで使うことができるセキュリティ層の両方です。 このドキュメントには、テスト環境にのみ推奨される安全でない例と、セキュリティ保護された展開に推奨される例の両方が含まれています。

<a href="" id="BKMK_browser"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">ブラウザーとクライアント デバイスのサポート</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_1" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

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

<a href="" id="BKMK_recm"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">推奨される (簡易) の展開</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_2" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access ゲートウェイは、Windows PowerShell コマンドレットを使用するか、サーバー マネージャーから開く役割および機能の追加ウィザードを使用して、Windows Server 2012 R2 または Windows Server 2012 を実行中のサーバーにインストールできます。 クイック インストールと構成では、このセクションで説明する Windows PowerShell コマンドレットを使用します。

-   [手順 1: Windows PowerShell Web Access をインストールします。](#BKMK_step1)

-   [手順 2: ゲートウェイを構成します。](#BKMK_step2)

-   [手順 3: 制限の厳しい承認規則を構成します。](#BKMK_step3)

<a href="" id="BKMK_step1"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 1: Windows PowerShell Web Access をインストールします。</span></a>

------------------------------------------------------------------------

#### Windows PowerShell コマンドレットを使って Windows PowerShell Web Access をインストールするには

1.  次のいずれかを実行して、管理者特権を使って Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

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
    <td><p>Windows PowerShell 3.0 と 4.0 では、サーバー マネージャー コマンドレット モジュールに含まれるコマンドレットを実行する前に、そのモジュールを Windows PowerShell セッションにインポートする必要はありません。 モジュールは、モジュールに含まれるコマンドレットを初めて実行するときに自動的にインポートされます。 また、Windows PowerShell のコマンドレットでは、大文字と小文字は区別されません。</p></td>
    </tr>
    </tbody>
    </table>

2.  次のように入力して **Enter** キーを押します。*computer_name* は、Windows PowerShell Web Access をインストールするリモート コンピューターの名前です (必要な場合)。 <span class="code">Restart</span> パラメーターによって、対象サーバーが自動的に再起動されます (必要な場合)。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_374a9c21-4f6e-471e-b957-bb190a594533'); "Copy to clipboard.")

        Install-WindowsFeature –Name WindowsPowerShellWebAccess -ComputerName <computer_name> -IncludeManagementTools -Restart

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
    <td><p>Windows PowerShell のコマンドレットを使って Windows PowerShell Web Access をインストールする場合、Web サーバー (IIS) の管理ツールは既定では追加されません。 Windows PowerShell Web Access ゲートウェイと同じサーバーに管理ツールをインストールする場合、<span class="code">IncludeManagementTools</span> パラメーターをインストール コマンドに追加します (この手順でも追加しています)。 Windows PowerShell Web Access の Web サイトをリモート コンピューターから管理する場合は、ゲートウェイの管理元となるコンピューターに <a href="http://go.microsoft.com/fwlink/?LinkID=304145">Remote Server Administration Tools for Windows 8.1</a> または <a href="http://go.microsoft.com/fwlink/p/?LinkID=238560">Remote Server Administration Tools for Windows 8</a> をインストールすることで、IIS マネージャー スナップインをインストールします。</p></td>
    </tr>
    </tbody>
    </table>

    オフライン VHD に役割や機能をインストールするには、<span class="code">ComputerName</span> パラメーターと <span class="code">VHD</span> パラメーターの両方を追加する必要があります。 <span class="code">ComputerName</span> パラメーターには VHD のマウント先のサーバー名が格納され、<span class="code">VHD</span> パラメーターには指定したサーバー上の VHD ファイルへのパスが格納されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_d841d509-347e-49d0-bf54-8d1f306bece6'); "Copy to clipboard.")

        Install-WindowsFeature –Name WindowsPowerShellWebAccess –VHD <path> -ComputerName <computer_name> -IncludeManagementTools -Restart

3.  インストールが完了したら、対象サーバーに Windows PowerShell Web Access がインストールされたことを確認します。これには、管理者特権で開いた Windows PowerShell コンソールで、対象サーバーに対して **Get-WindowsFeature** コマンドレットを実行します。 また、サーバー マネージャー コンソールに Windows PowerShell Web Access がインストールされたことも確認できます。これには、**[すべてのサーバー]** ページで対象サーバーを選択し、選択したサーバーの **[役割と機能]** タイルを表示します。 Windows PowerShell Web Access の Readme ファイルも表示できます。

4.  Windows PowerShell Web Access がインストールされると、基本的かつ必須のゲートウェイ セットアップ手順を記載した Readme ファイルの確認を求めるメッセージが表示されます。 これらのセットアップ手順は、次の「[手順 2. ゲートウェイを構成する](#BKMK_step2)」セクションでも説明します。 Readme ファイルのパスは <span class="computerOutputInline">C:\\Windows\\Web\\PowerShellWebAccess\\wwwroot\\README.txt</span> です。

<a href="" id="BKMK_step2"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 2: ゲートウェイを構成します。</span></a>

------------------------------------------------------------------------

**Install-PswaWebApplication** コマンドレットを使うと Windows PowerShell Web Access を簡単に構成できます。 <span class="code">UseTestCertificate</span> パラメーターを <span class="code">Install-PswaWebApplication</span> コマンドレットに追加することで、テスト用の自己署名 SSL 証明書をインストールできますが、これは安全ではありません。運用環境のセキュリティ保護のため、常に証明機関 (CA) によって署名された有効な SSL 証明書を使ってください。 管理者は IIS マネージャー コンソールを使ってテスト証明書を任意の署名済み証明書に置き換えることができます。

Windows PowerShell Web Access の Web アプリケーションの構成を完了するには、<span class="code">Install-PswaWebApplication</span> コマンドレットを実行するか、IIS マネージャーの GUI ベースの構成手順を実行します。 既定では、コマンドレットによって **pswa** Web アプリケーション (および専用の **pswa_pool** というアプリケーション プール) が **[既定の Web サイト]** コンテナーにインストールされ、IIS マネージャーで確認できます。必要な場合、コマンドレットに指定して Web アプリケーションの既定のサイト コンテナーを変更できます。 IIS マネージャーは、ポート番号や Secure Sockets Layer (SSL) 証明書の変更など、Web アプリケーションで利用できる構成オプションを提供します。

<table>
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th><span><img src="https://i-technet.sec.s-msft.com/dynimg/IC17938.jpeg" title="System_CAPS_security" alt="System_CAPS_security" id="s-e6f6a65cf14f462597b64ac058dbe1d0-system-media-system-caps-security" /></span><span class="alertTitle"> セキュリティ メモ </span></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>管理者には、CA によって署名された有効な証明書を使うようゲートウェイを構成することを強くお勧めします。</p></td>
</tr>
</tbody>
</table>

-   [Windows PowerShell Web Access ゲートウェイを Install-pswawebapplication を使用して、テスト証明書を構成するには](#BKMK_testcert)

-   [Windows PowerShell Web Access ゲートウェイを Install-pswawebapplication と IIS マネージャーを使用して正規の証明書を構成するには](#BKMK_gencert)

#### Install-PswaWebApplication を使って、テスト証明書を使う Windows PowerShell Web Access Web ゲートウェイを構成するには

1.  次のいずれかを実行して Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** をクリックします。

2.  次のように入力して **Enter** キーを押します。

    **Install-pswawebapplication UseTestCertificate**

    <table>
    <colgroup>
    <col width="100%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th><span><img src="https://i-technet.sec.s-msft.com/dynimg/IC17938.jpeg" title="System_CAPS_security" alt="System_CAPS_security" id="s-e6f6a65cf14f462597b64ac058dbe1d0-system-media-system-caps-security" /></span><span class="alertTitle"> セキュリティに関する注意 </span></th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td><p><span class="code">UseTestCertificate</span> パラメーターはプライベートなテスト環境でのみ使ってください。 運用環境のセキュリティ保護のため、CA によって署名された有効な証明書を使うことをお勧めします。</p></td>
    </tr>
    </tbody>
    </table>

    コマンドレットを実行すると、IIS の [既定の Web サイト] コンテナーに Windows PowerShell Web Access の Web アプリケーションがインストールされます。 このコマンドレットにより、既定の Web サイト (https://&lt;server_name&gt;/pswa) で Windows PowerShell Web Access を実行するために必要なインフラストラクチャが作成されます。 Web アプリケーションを別の Web サイトにインストールするには、<span class="code">WebSiteName</span> パラメーターを追加して Web サイトの名前を入力します。 Web アプリケーションの名前 (既定値は <span class="code">pswa</span>) を変更するには、<span class="code">WebApplicationName</span> パラメーターを追加します。

    コマンドレットの実行により次の設定が構成されます。 これらの設定は、必要に応じて IIS マネージャー コンソールで変更できます。

    -   Path: /pswa

    -   ApplicationPool: pswa_pool

    -   EnabledProtocols: http

    -   PhysicalPath: %*windir*%/Web/PowerShellWebAccess/wwwroot

    <span class="label">例:</span> <span class="code">Install-pswawebapplication – webApplicationName/mywebapp となります: useTestCertificate</span>

    この例では、Windows PowerShell Web Access の最終的な Web サイトは https://&lt;*server_name*&gt;/myWebApp になります。

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
    <td><p>サインインできるのは、承認規則の追加によって Web サイトへのアクセスが許可されたユーザーだけです。 詳細については、「<a href="#BKMK_step3">手順 3. 制限的な承認規則を構成する</a>」と「<a href="https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx">Windows PowerShell Web Access の承認規則とセキュリティ機能</a>」を参照してください。</p></td>
    </tr>
    </tbody>
    </table>

#### 正規の証明書を持つ Windows PowerShell Web Access ゲートウェイを Install-PswaWebApplication と IIS Manager を使用して構成するには

1.  次のいずれかを実行して Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** をクリックします。

2.  次のように入力して **Enter** キーを押します。

    **Install-pswawebapplication**

    コマンドレットの実行により次のゲートウェイ設定が構成されます。 これらの設定は、必要に応じて IIS マネージャー コンソールで変更できます。 <span class="code">WebsiteName</span> コマンドレットの <span class="code">WebApplicationName</span> および <span class="code">Install-PswaWebApplication</span> パラメーターの値を指定することも可能です。

    -   Path: /pswa

    -   ApplicationPool: pswa_pool

    -   EnabledProtocols: http

    -   PhysicalPath: %*windir*%/Web/PowerShellWebAccess/wwwroot

3.  次のいずれかの方法で IIS マネージャー コンソールを開きます。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。 [サーバー マネージャー] の **[ツール]** メニューで **[インターネット インフォメーション サービス (IIS) マネージャー]** をクリックします。

    -   Windows の**スタート**画面で、**[サーバー マネージャー]** をクリックします。

4.  IIS マネージャーのツリー ウィンドウで、Windows PowerShell Web Access がインストールされているサーバーのノードを展開し、**[サイト]** フォルダーを表示させます。 **[サイト]** フォルダーを展開します。

5.  Windows PowerShell Web Access の Web アプリケーションをインストールした Web サイトを選択します。 **[操作]** ウィンドウで、**[バインド]** をクリックします。

6.  **[サイト バインド]** ダイアログ ボックスの **[追加]** をクリックします。

7.  **[サイト バインドの追加]** ダイアログ ボックスの **[種類]** フィールドで、**[https]** をクリックします。

8.  **"SSL 証明書"** フィールドのドロップダウン メニューから、署名済み証明書を選択します。 **[OK]** をクリックします。 証明書の取得方法の詳細については、このトピックの「[IIS マネージャーで SSL 証明書を構成するには](#BKMK_cert)」を参照してください。

    これで、Windows PowerShell Web Access の Web アプリケーションが署名済み SSL 証明書を使うよう構成されました。 ブラウザー ウィンドウで https://&lt;server_name&gt;/pswa を開くと Windows PowerShell Web Access にアクセスできます。

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
    <td><p>サインインできるのは、承認規則の追加によって Web サイトへのアクセスが許可されたユーザーだけです。 詳細については、「<a href="#BKMK_step3">手順 3. 制限的な承認規則を構成する</a>」と「<a href="https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx">Windows PowerShell Web Access の承認規則とセキュリティ機能</a>」を参照してください。</p></td>
    </tr>
    </tbody>
    </table>

<a href="" id="BKMK_step3"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 3: 制限の厳しい承認規則を構成します。</span></a>

------------------------------------------------------------------------

Windows PowerShell Web Access をインストールし、ゲートウェイを構成すると、ユーザーがブラウザーでサインイン ページを開けるようになります。ただし、Windows PowerShell Web Access 管理者によってアクセスを明示的に許可されない限りサインインできません。 Windows PowerShell Web Access のアクセス制御は、次の表に示す一連の Windows PowerShell コマンドレットによって管理します。 承認規則の追加と管理に関しては、相当する GUI はありません。 Windows PowerShell Web Access コマンドレットの詳細については、コマンドレット リファレンス トピック「[Windows PowerShell Web Access Cmdlets](https://technet.microsoft.com/library/hh918342.aspx)」(Windows PowerShell Web Access コマンドレット) を参照してください。

Windows PowerShell Web Access の承認規則とセキュリティの詳細については、「[Windows PowerShell Web Access の承認規則とセキュリティ機能](https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx)」を参照してください。

#### 制限的な承認規則を追加するには

1.  次のいずれかを実行して、管理者特権を使って Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

2.  <span class="label">セッション構成を使用してユーザーのアクセスを制限するためのオプション手順:</span> 使用する場合、規則で既にセッション構成があることを確認します。 まだ作成されていない場合は、MSDN の「[about_Session_Configuration_Files](https://msdn.microsoft.com/library/windows/desktop/hh847838.aspx)」に記載されているセッション構成の作成手順を使用してください。

3.  次のように入力して **Enter** キーを押します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_f9e7959b-75d0-4d63-8f8e-02334a8dd09d'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName <domain\user | computer\user> -ComputerName <computer_name> -ConfigurationName <session_configuration_name>

    この承認規則により、特定のユーザー 1 人が、通常アクセスが許可されているネットワーク上の 1 つのコンピューターにアクセスして、このユーザーが通常必要とするスクリプトとコマンドレットに制限された特定の 1 つのセッション構成にアクセスできるようになります。 次の例では、<span class="code">Contoso</span> ドメインの <span class="code">JSmith</span> というユーザーに、<span class="code">Contoso_214</span> というコンピューターを管理し、<span class="code">NewAdminsOnly</span> というセッション構成を使うためのアクセス権が付与されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_ebd5bc5e-ec5d-4955-a86a-63843e480e37'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName Contoso\JSmith -ComputerName Contoso_214 -ConfigurationName NewAdminsOnly

4.  いずれかを実行して、ルールが作成されたことを確認、 **Get-pswaauthorizationrule** コマンドレット、または **Test-pswaauthorizationrule UserName &lt;ドメイン \\ ユーザー | computer\\user&gt; -computername** &lt;computer_name&gt;します。 たとえば、 **Test-pswaauthorizationrule – UserName Contoso\\JSmith – ComputerName Contoso_214**します。

承認規則が構成されたため、承認されたユーザーは Web ベースのコンソールにサインインして Windows PowerShell Web Access の使用を開始できます。

<a href="" id="BKMK_custom"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">カスタムの配置</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_3" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access ゲートウェイは、サーバー マネージャーの役割および機能の追加ウィザードを使用して、Windows Server 2012 R2 または Windows Server 2012 を実行中のサーバーにインストールできます。 Windows PowerShell Web Access をインストールした後、IIS Manager でゲートウェイの構成をカスタマイズできます。

<a href="" id="BKMK_custom1"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 1: Windows PowerShell Web Access をインストールします。</span></a>

------------------------------------------------------------------------

#### 役割と機能の追加ウィザードを使って Windows PowerShell Web Access をインストールするには

1.  サーバー マネージャーを既に開いている場合は、次の手順に進んでください。 サーバー マネージャーをまだ開いていない場合は、次のいずれかの方法で開きます。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。

    -   Windows の**スタート**画面で、**[サーバー マネージャー]** をクリックします。

2.  **[管理]** メニューの **[役割と機能の追加]** をクリックします。

3.  **[インストールの種類の選択]** ページで **[役割ベースまたは機能ベースのインストール]** を選択します。 **[次へ]** をクリックします。

4.  **[対象サーバーの選択]** ページで、サーバー プールからサーバーを選択するか、オフライン VHD を選択します。 対象サーバーとしてオフライン VHD を選択するには、まず VHD マウント先のサーバーを選択してから、VHD ファイルを選択します。 サーバーをサーバー プールに追加する方法については、サーバー マネージャーのヘルプを参照してください。 対象サーバーを選択したら、**[次へ]** をクリックします。

5.  ウィザードの **[機能の選択]** ページで **[Windows PowerShell]** を展開し、**[Windows PowerShell Web Access]** を選択します。

6.  .NET Framework 4.5 や Web サーバー (IIS) の役割サービスなど、必要な機能の追加を求めるメッセージが表示されます。 必要な機能を追加して続行します。

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
    <td><p>Windows PowerShell Web Access を使って役割および機能の追加ウィザードをインストールすると、IIS マネージャー スナップインを含む Web サーバー (IIS) もインストールされます。 このスナップインとその他の IIS 管理ツールは、役割および機能の追加ウィザードを使う場合に既定でインストールされます。 後の手順で説明する Windows PowerShell コマンドレットを使って Windows PowerShell Web Access をインストールする場合、これらの管理ツールは既定では追加されません。</p></td>
    </tr>
    </tbody>
    </table>

7.  **[インストール オプションの確認]** ページで、手順 4. で選択した対象サーバーに Windows PowerShell Web Access の機能のファイルが保存されていない場合、**[代替ソース パスの指定]** をクリックして機能ファイルのパスを入力します。 それ以外の場合、**[インストール]** をクリックします。

8.  **[インストール]** をクリックすると、**[インストールの進行状況]** ページには、インストールの進行状況、結果、メッセージ (警告、エラー、Windows PowerShell Web Access に必要なインストール後の構成手順など) が表示されます。 Windows PowerShell Web Access がインストールされると、基本的かつ必須のゲートウェイ セットアップ手順を記載した Readme ファイルの確認を求めるメッセージが表示されます。 これらの手順も、このドキュメントで説明しています。 Readme ファイルのパスは <span class="computerOutputInline">C:\\Windows\\Web\\PowerShellWebAccess\\wwwroot\\README.txt</span> です。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 2: ゲートウェイを構成します。</span></a>

------------------------------------------------------------------------

このセクションの手順は、Windows PowerShell Web Access の Web アプリケーションを Web サイトのルート ディレクトリではなくサブディレクトリにインストールするための手順です。 この手順では、<span class="code">Install-PswaWebApplication</span> コマンドレットで実行する操作に相当する内容を GUI ベースで実行しています。 さらにこのセクションでは、IIS マネージャーを使って Windows PowerShell Web Access ゲートウェイをルート Web サイトとして構成するための手順も説明します。

-   [IIS マネージャーを使用して、既存の web サイトで、ゲートウェイを構成するには](#BKMK_configman)

-   [IIS マネージャーを使用して、テスト証明書とルート web サイトとして、ゲートウェイを構成するには](#BKMK_configroot)

-   

#### IIS マネージャーを使って既存の Web サイトにゲートウェイを構成するには

1.  次のいずれかの方法で IIS マネージャー コンソールを開きます。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。 [サーバー マネージャー] の **[ツール]** メニューで **[インターネット インフォメーション サービス (IIS) マネージャー]** をクリックします。

    -   Windows の**スタート**画面で、**インターネット インフォメーション サービス (IIS) マネージャー**という名前の任意の一部を入力します。 **[アプリ]** の結果に表示されたショートカットをクリックします。

2.  Windows PowerShell Web Access 用の新しいアプリケーション プールを作成します。 IIS マネージャーのツリー ウィンドウでゲートウェイ サーバーのノードを展開して、**[アプリケーション プール]** を選択し、**[操作]** ウィンドウの **[アプリケーション プールの追加]** をクリックします。

3.  新しいアプリケーション プールを **pswa_pool** という名前で追加するか、別の名前を指定して追加します。 **[OK]** をクリックします。

4.  IIS マネージャーのツリー ウィンドウで、Windows PowerShell Web Access がインストールされているサーバーのノードを展開し、**[サイト]** フォルダーを表示させます。 **[サイト]** フォルダーを選択します。

5.  Windows PowerShell Web Access の Web サイトの追加先となる Web サイト (**[既定の Web サイト]** など) を右クリックして、**[アプリケーションの追加]** をクリックします。

6.  **"エイリアス"** フィールドに「pswa」と入力するか、別のエイリアスを入力します。 このエイリアスが仮想ディレクトリ名となります。 たとえば、次の URL の **pswa** は、この手順で指定されたエイリアスを表します。https://&lt;server_name&gt;/pswa

7.  **"アプリケーション プール"** フィールドで、手順 3. で作成したアプリケーション プールを選択します。

8.  **"物理パス"** フィールドで、アプリケーションの場所を参照します。 既定の場所 (%windir%/Web/PowerShellWebAccess/wwwroot) を使うことができます。 **[OK]** をクリックします。

9.  このトピックの「[IIS マネージャーで SSL 証明書を構成するには](#BKMK_cert)」の説明に従って手順を実行します。

10. <span class="label">省略可能なセキュリティ手順:</span> ツリー ペインで選択した web サイト、ダブルクリック **SSL 設定** コンテンツ ペインにします。 **[SSL が必要]** を選択して、**[操作]** ウィンドウで **[適用]** をクリックします。 必要に応じて、**[SSL 設定]** ウィンドウを使って、Windows PowerShell Web Access の Web サイトに接続するユーザーに対してクライアント証明書の所有を要求できます。 クライアント証明書により、クライアント デバイスのユーザーの ID を検証できます。 クライアント証明書の要求によって Windows PowerShell Web Access のセキュリティを強化する方法の詳細については、このトピックの「[Windows PowerShell Web Access の承認規則とセキュリティ機能](https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx)」を参照してください。

11. クライアント デバイスでブラウザー セッションを開きます。 サポートされるブラウザーとデバイスの詳細については、このトピックの「[ブラウザーとクライアント デバイスのサポート](#BKMK_browser)」を参照してください。

12. 新しい Windows PowerShell Web Access Web サイト https://&lt;*gateway_server_name*&gt;/pswa を開きます。

    ブラウザーには Windows PowerShell Web Access コンソールのサインイン ページが表示されます。

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
    <td><p>サインインできるのは、承認規則の追加によって Web サイトへのアクセスが許可されたユーザーだけです。</p></td>
    </tr>
    </tbody>
    </table>

13. 管理者特権で開かれた ([管理者として実行]) Windows PowerShell セッションで、次のスクリプトを実行します。*application_pool_name* は、手順 3. で作成したアプリケーション プールの名前です。これによって承認ファイルへのアクセス権がアプリケーション プールに付与されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_c1a80a93-8fcf-4beb-a025-5f81bfb8bdae'); "Copy to clipboard.")

        $applicationPoolName = "<application_pool_name>"
        $authorizationFile = "C:\windows\web\powershellwebaccess\data\AuthorizationRules.xml"
        c:\windows\system32\icacls.exe $authorizationFile /grant ('"' + "IIS AppPool\$applicationPoolName" + '":R') > $null

    承認ファイルに対する既存のアクセス権を表示するには、次のコマンドを実行します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_ac2179c2-9548-4a17-8663-267fdd105380'); "Copy to clipboard.")

        c:\windows\system32\icacls.exe $authorizationFile

#### IIS マネージャーを使用してテスト証明書を持つゲートウェイをルート Web サイトとして構成するには

1.  次のいずれかの方法で IIS マネージャー コンソールを開きます。

    -   Windows デスクトップで、Windows タスク バーの **[サーバー マネージャー]** をクリックし、サーバー マネージャーを開始します。 [サーバー マネージャー] の **[ツール]** メニューで **[インターネット インフォメーション サービス (IIS) マネージャー]** をクリックします。

    -   Windows の**スタート**画面で、**インターネット インフォメーション サービス (IIS) マネージャー**という名前の任意の一部を入力します。 **[アプリ]** の結果に表示されたショートカットをクリックします。

2.  IIS マネージャーのツリー ウィンドウで、Windows PowerShell Web Access がインストールされているサーバーのノードを展開し、**[サイト]** フォルダーを表示させます。 **[サイト]** フォルダーを選択します。

3.  **[操作]** ウィンドウで **[Web サイトの追加]** をクリックします。

4.  Web サイトの名前 (**Windows PowerShell Web Access** など) を入力します。

5.  新しい Web サイト用のアプリケーション プールが自動で作成されます。 別のアプリケーション プールを使うには、**[選択]** をクリックして、新しい Web サイトに関連付けるアプリケーション プールを選択します。 **[アプリケーション プールの選択]** ダイアログ ボックスで別のアプリケーション プールを選択し、**[OK]** をクリックします。

6.  **[物理パス]** テキスト ボックスで、%*windir*%/Web/PowerShellWebAccess/wwwroot に移動します。

7.  **[バインド]** 領域の **[種類]** フィールドで、**[https]** を選択します。

8.  別のサイトまたはアプリケーションがまだ使っていないポート番号を Web サイトに割り当てます。 未使用のポートを調べるには、コマンド プロンプト ウィンドウで **netstat** コマンドを実行します。 既定のポート番号は 443 です。

    別の Web サイトが既に 443 を使っている場合、またはポート番号を変更するセキュリティ上の理由がある場合は、既定のポートを変更します。 選択したポートを、ゲートウェイ サーバー上で実行されている別の Web サイトが使っている場合は、**[Web サイトの追加]** ダイアログ ボックスで **[OK]** をクリックすると警告が表示されます。 Windows PowerShell Web Access を実行するには未使用のポートを使う必要があります。

9.  組織の必要に応じて、組織またはユーザーがわかりやすいホスト名を指定します (**www.contoso.com** など)。 **[OK]** をクリックします。

10. 運用環境のセキュリティ保護を強化するため、CA によって署名された有効な証明書を提供することを強くお勧めします。 ユーザーは HTTPS Web サイトを経由しないと Windows PowerShell Web Access に接続できないため、SSL 証明書は必ず提供する必要があります。 証明書の取得方法の詳細については、このトピックの「[IIS マネージャーで SSL 証明書を構成するには](#BKMK_cert)」を参照してください。

11. **[OK]** をクリックして **[Web サイトの追加]** ダイアログ ボックスを閉じます。

12. 管理者特権で開かれた ([管理者として実行]) Windows PowerShell セッションで、次のスクリプトを実行します。*application_pool_name* は、手順 4. で作成したアプリケーション プールの名前です。これによって承認ファイルへのアクセス権がアプリケーション プールに付与されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_35ae9944-ca44-4af7-9c96-616083b3e3db'); "Copy to clipboard.")

        $applicationPoolName = "<application_pool_name>"
        $authorizationFile = "C:\windows\web\powershellwebaccess\data\AuthorizationRules.xml"
        c:\windows\system32\icacls.exe $authorizationFile /grant ('"' + "IIS AppPool\$applicationPoolName" + '":R') > $null

    承認ファイルに対する既存のアクセス権を表示するには、次のコマンドを実行します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_0eb6d70a-2807-498b-b088-fa5233ed68d5'); "Copy to clipboard.")

        c:\windows\system32\icacls.exe $authorizationFile

13. IIS マネージャーのツリー ウィンドウで新しい Web サイトが選択された状態で、**[操作]** ウィンドウの **[開始]** をクリックすると、Web サイトが開設されます。

14. クライアント デバイスでブラウザー セッションを開きます。 サポートされるブラウザーとデバイスの詳細については、このドキュメントの「[ブラウザーとクライアント デバイスのサポート](#BKMK_browser)」を参照してください。

15. 新しい Windows PowerShell Web Access の Web サイトを開きます。

    ルート Web サイトは Windows PowerShell Web Access フォルダーを参照しているため、https://&lt;*gateway_server_name*&gt; を開くと、ブラウザーには Windows PowerShell Web Access のサインイン ページが表示されます。 URL に **/pswa** を追加する必要はありません。

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
    <td><p>サインインできるのは、承認規則の追加によって Web サイトへのアクセスが許可されたユーザーだけです。</p></td>
    </tr>
    </tbody>
    </table>

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">手順 3: 制限の厳しい承認規則を構成します。</span></a>

------------------------------------------------------------------------

Windows PowerShell Web Access をインストールし、ゲートウェイを構成すると、ユーザーがブラウザーでサインイン ページを開けるようになります。ただし、Windows PowerShell Web Access 管理者によってアクセスを明示的に許可されない限りサインインできません。 Windows PowerShell Web Access のアクセス制御は、次の表に示す一連の Windows PowerShell コマンドレットによって管理します。 承認規則の追加と管理に関しては、相当する GUI はありません。 Windows PowerShell Web Access コマンドレットの詳細については、コマンドレット リファレンス トピック「[Windows PowerShell Web Access Cmdlets](https://technet.microsoft.com/library/hh918342.aspx)」(Windows PowerShell Web Access コマンドレット) を参照してください。

Windows PowerShell Web Access の承認規則とセキュリティの詳細については、「[Windows PowerShell Web Access の承認規則とセキュリティ機能](https://technet.microsoft.com/en-us/library/dn282394(v=ws.11).aspx)」を参照してください。

#### 制限的な承認規則を追加するには

1.  次のいずれかを実行して、管理者特権を使って Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

2.  <span class="label">セッション構成を使用してユーザーのアクセスを制限するためのオプション手順:</span> 使用する場合、規則で既にセッション構成があることを確認します。 まだ作成されていない場合は、MSDN の「[about_Session_Configuration_Files](https://msdn.microsoft.com/library/windows/desktop/hh847838.aspx)」に記載されているセッション構成の作成手順を使用してください。

3.  次のように入力して **Enter** キーを押します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_4df22c91-f56f-4bb5-91e7-99f9b365ed5d'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName <domain\user | computer\user> -ComputerName <computer_name> -ConfigurationName <session_configuration_name>

    この承認規則により、特定のユーザー 1 人が、通常アクセスが許可されているネットワーク上の 1 つのコンピューターにアクセスして、このユーザーが通常必要とするスクリプトとコマンドレットに制限された特定の 1 つのセッション構成にアクセスできるようになります。 次の例では、<span class="code">Contoso</span> ドメインの <span class="code">JSmith</span> というユーザーに、<span class="code">Contoso_214</span> というコンピューターを管理し、<span class="code">NewAdminsOnly</span> というセッション構成を使うためのアクセス権が付与されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_efc3999a-2905-453f-86cd-014b41658ffc'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName Contoso\JSmith -ComputerName Contoso_214 -ConfigurationName NewAdminsOnly

4.  いずれかを実行して、ルールが作成されたことを確認、 **Get-pswaauthorizationrule** コマンドレット、または **Test-pswaauthorizationrule UserName &lt;ドメイン \\ ユーザー | computer\\user&gt; -computername** &lt;computer_name&gt;します。 たとえば、 **Test-pswaauthorizationrule – UserName Contoso\\JSmith – ComputerName Contoso_214**します。

承認規則が構成されたため、承認されたユーザーは Web ベースのコンソールにサインインして Windows PowerShell Web Access の使用を開始できます。

<a href="" id="BKMK_configcert"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">正規の証明書を構成します。</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_4" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

運用環境のセキュリティ保護のため、証明機関 (CA) によって署名されている有効な SSL 証明書を常に使用してください。 このセクションの手順では、有効な SSL 証明書を CA から取得して適用する方法について説明します。

### IIS マネージャーで SSL 証明書を構成するには

1.  IIS マネージャーのツリー ウィンドウで、Windows PowerShell Web Access がインストールされているサーバーを選択します。

2.  コンテンツ ウィンドウで **[サーバー証明書]** をダブルクリックします。

3.  **[操作]** ウィンドウで、次のいずれかを実行します。 IIS でサーバー証明書を構成する方法の詳細については、「[IIS 7 でサーバー証明書を構成する](https://technet.microsoft.com/library/cc732230.aspx)」を参照してください。

    -   **[インポート]** をクリックして、ネットワーク内の場所にある既存の有効な証明書をインポートします。

    -   **[証明書の要求の作成]** をクリックして、VeriSign™、Thawte、GeoTrust® などの CA に証明書を要求します。 証明書の共通名が要求のホスト ヘッダーと一致している必要があります。 たとえば、クライアントのブラウザーが http://www.contoso.com/ を要求する場合、共通名も http://www.contoso.com/ でなければなりません。 これは、証明書を使う Windows PowerShell Web Access ゲートウェイを提供する場合に最も安全かつ推奨されるオプションです。

    -   **[自己署名証明書を作成する]** をクリックして証明書を作成します。この証明書はすぐに使うことができ、必要な場合は後でCAが署名できます。 自己署名証明書にわかりやすい名前を指定します (**Windows PowerShell Web Access** など)。 これは、安全と見なされない、プライベートなテスト環境にのみ推奨されるオプションです。

4.  証明書を作成または取得した後、IIS マネージャーのツリー ウィンドウで、証明書を適用する Web サイト (**[既定の Web サイト]** など) を選択して、**[操作]** ウィンドウの **[バインド]** をクリックします。

5.  **[サイト バインドの追加]** ダイアログ ボックスで、サイトの **[https]** バインドを追加します (表示されていない場合)。 自己署名証明書を使っていない場合、手順 3. のホスト名を指定します。 自己署名証明書を使っている場合、この手順は必要ありません。

6.  手順 3. で取得または作成した証明書を選択して、**[OK]** をクリックします。

<a href="" id="BKMK_using"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">Web ベースの Windows PowerShell コンソールを使用します。</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_5" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

このトピックの説明に従って Windows PowerShell Web Access のインストールとゲートウェイ構成が完了したら、Web ベースの Windows PowerShell コンソールを使えるようになります。 Web ベース コンソールの概要については、「[Web ベースの Windows PowerShell コンソールの使用](https://technet.microsoft.com/en-us/library/hh831417(v=ws.11).aspx)」を参照してください。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">関連項目</span></a>
<a href="/en-us/library/hh831611(v=ws.11).aspx#Anchor_6" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

[インターネット インフォメーション サービス (IIS) 7.0 のドキュメント](https://technet.microsoft.com/library/cc753433.aspx)
[IIS マネージャー 7.0 ヘルプ](https://technet.microsoft.com/library/cc732664.aspx)
[Web サーバーのセキュリティを構成する (IIS 7)](https://technet.microsoft.com/library/cc731278.aspx)
[IPsec 展開のリソース](https://technet.microsoft.com/network/bb531150)

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


