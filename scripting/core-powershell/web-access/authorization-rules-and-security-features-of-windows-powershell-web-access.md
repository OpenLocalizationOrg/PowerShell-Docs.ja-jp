---
title: "Windows PowerShell Web Access の承認規則とセキュリティ機能"
ms.date: 2016-05-11
keywords: "PowerShell, コマンドレット"
description: 
ms.topic: article
author: jpjofre
manager: dongill
ms.prod: powershell
translationtype: Human Translation
ms.sourcegitcommit: a656ec981dc03fd95c5e70e2d1a2c741ee1adc9b
ms.openlocfilehash: dc50a729855a71d61c187da9d698bd294f740546

---

# Windows PowerShell Web Access の承認規則とセキュリティ機能

最終更新日 : 2013 年 6 月 24 日

適用対象: Windows Server 2012 R2、Windows Server 2012

Windows Server® 2012 R2 および Windows Server® 2012 の Windows PowerShell® Web Access のセキュリティ モデルには制限があります。 ユーザーが Windows PowerShell Web Access ゲートウェイにサインインし、Web ベースの Windows PowerShell コンソールを使用するには、アクセス許可が明示的に付与されている必要があります。

-   [承認規則とサイトのセキュリティ構成](#BKMK_auth)

-   [セッション管理](#BKMK_sesmgmt)


Windows PowerShell Web Access をインストールし、ゲートウェイを構成すると、ユーザーがブラウザーでサインイン ページを開けるようになります。ただし、Windows PowerShell Web Access 管理者によってアクセスを明示的に許可されない限りサインインできません。 Windows PowerShell Web Access のアクセス制御は、次の表に示す一連の Windows PowerShell コマンドレットによって管理します。 承認規則の追加と管理に関しては、相当する GUI はありません。 Windows PowerShell Web Access コマンドレットの詳細については、コマンドレット リファレンス トピック「[Windows PowerShell Web Access Cmdlets](https://technet.microsoft.com/library/hh918342.aspx)」(Windows PowerShell Web Access コマンドレット) を参照してください。

管理者は、Windows PowerShell Web Access に 0 個から *n* 個の認証規則を定義できます。 既定のセキュリティは、許容的ではなく制限的なものになります。認証規則がゼロの場合には、すべてのユーザーがすべてのアクセスを禁止されます。

Windows Server 2012 R2 の Add-PswaAuthorizationRule および Test-PswaAuthorizationRule に、リモート コンピューターから、またはアクティブな Windows PowerShell Web Access セッション内から Windows PowerShell Web Access 承認規則を追加およびテストできるようにするために、資格情報パラメーターが用意されました。 資格情報パラメーターを持つ他の Windows PowerShell コマンドレットと同様、PSCredential オブジェクトをパラメーターの値として指定できます。 リモート コンピューターに渡す資格情報を含む PSCredential オブジェクトを作成するには、[Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを実行します。

Windows PowerShell Web Access の認証規則はホワイトリスト方式です。 各規則は、ユーザー、対象コンピューター、および指定された対象コンピューターにおける特定のWindows PowerShell [セッション構成](https://technet.microsoft.com/library/dd819508.aspx) (エンドポイントまたは実行空間とも呼ばれます) の間に許可される接続を定義したものです。

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
<td><p>アクセスを許可されるためにユーザーが従う必要がある規則は 1 つだけです。 1 つのコンピューターに対する全言語でのアクセス、または Windows PowerShell リモート管理コマンドレットだけに対するアクセスを許可されているユーザーは、Web ベース コンソールから、最初の対象コンピューターに接続されているその他のコンピューターにログオン (ホップ) できます。 Windows PowerShell Web Access の最も安全な構成方法は、制約付きセッション構成 (エンドポイントまたは実行空間とも呼ばれます) に対するアクセスだけを許可し、通常リモートでの実行が必要になる特定のタスクを完了できるようにする方法です。</p></td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>名前</p></th>
<th><p>説明</p></th>
<th><p>パラメーター</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><a href="https://technet.microsoft.com/library/jj592890.aspx">Add-PswaAuthorizationRule</a></p></td>
<td><p>Windows PowerShell Web Access 承認規則のセットに新しい承認規則を追加します。</p></td>
<td><ul>
<li><p>ComputerGroupName</p></li>
<li><p>ComputerName</p></li>
<li><p>ConfigurationName</p></li>
<li><p>RuleName</p></li>
<li><p>UserGroupName</p></li>
<li><p>UserName</p></li>
<li><p>資格情報 (Windows Server 2012 R2 以降)</p></li>
</ul></td>
</tr>
<tr class="even">
<td><p><a href="https://technet.microsoft.com/library/jj592893.aspx">Remove-PswaAuthorizationRule</a></p></td>
<td><p>指定された承認規則を Windows PowerShell Web Access から削除します。</p></td>
<td><ul>
<li><p>Id</p></li>
<li><p>RuleName</p></li>
</ul></td>
</tr>
<tr class="odd">
<td><p><a href="https://technet.microsoft.com/library/jj592891.aspx">Get-PswaAuthorizationRule</a></p></td>
<td><p>Windows PowerShell Web Access の承認規則のセットを返します。 パラメーターなしで使われた場合、このコマンドレットはすべての規則を返します。</p></td>
<td><ul>
<li><p>Id</p></li>
<li><p>RuleName</p></li>
</ul></td>
</tr>
<tr class="even">
<td><p><a href="https://technet.microsoft.com/library/jj592892.aspx">Test-PswaAuthorizationRule</a></p></td>
<td><p>承認規則を評価して、特定のユーザー、コンピューター、またはセッション構成のアクセス要求が承認されるかどうかを判定します。 既定では、パラメーターが追加されていない場合、このコマンドレットはすべての承認規則を評価します。 管理者は、パラメーターを追加して、テスト対象の承認規則または規則のサブセットを指定できます。</p></td>
<td><ul>
<li><p>ComputerName</p></li>
<li><p>ConfigurationName</p></li>
<li><p>RuleName</p></li>
<li><p>UserName</p></li>
<li><p>資格情報 (Windows Server 2012 R2 以降)</p></li>
</ul></td>
</tr>
</tbody>
</table>

これらのコマンドレットによって作成されるアクセス規則のセットを使って、Windows PowerShell Web Access ゲートウェイでユーザーを承認できます。 これらの規則は、対象コンピューター上のアクセス制御リスト (ACL) とは別に、Web アクセスに対する追加のセキュリティ層として機能します。 以降のセクションでは、セキュリティについてさらに詳しく説明します。

先に説明したセキュリティ層のいずれかを通過できない場合、ユーザーのブラウザー ウィンドウに通常の "アクセス拒否" メッセージが表示されます。 ゲートウェイ サーバーには詳細なセキュリティ情報が記録されますが、エンド ユーザーに対しては、通過したセキュリティ層の数や、サインインまたは認証のエラーが発生した具体的な層などの情報は表示されません。

承認規則の構成方法の詳細については、このトピックの「[承認規則の構成](#BKMK_configrules)」を参照してください。

<a href="" id="BKMK_sec"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">セキュリティ</span></a>

------------------------------------------------------------------------

Windows PowerShell Web Access のセキュリティ モデルは、Web ベース コンソールのエンド ユーザーと対象コンピューターの間に 4 つの層を構成します。 Windows PowerShell Web Access 管理者は、IIS マネージャー コンソールで構成を追加することで、セキュリティ層をさらに追加できます。 IIS マネージャー コンソールで Web サイトを保護する方法の詳細については、「[Configure Web Server Security (IIS 7) (Web サーバーのセキュリティを構成する (IIS 7))](https://technet.microsoft.com/library/cc731278(v=ws.10).aspx)」を参照してください。 IIS のベスト プラクティスと、サービス拒否攻撃を阻止する方法の詳細については、[DoS/サービス拒否攻撃阻止のベスト プラクティスに関するページ](https://technet.microsoft.com/library/cc750213.aspx)を参照してください。 管理者は、市販の認証ソフトウェアを購入してインストールすることも可能です。

エンド ユーザーと対象コンピューターの間に構成される 4 つの層を次の表に示します。

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th><p>順番</p></th>
<th><p>層</p></th>
<th><p>説明</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>1 で保護されたプロセスとして起動されました</p></td>
<td><p>Web サーバー (IIS) のセキュリティ機能 (クライアント証明書の認証など)</p></td>
<td><p>Windows PowerShell Web Access のユーザーは、常にゲートウェイでユーザー名とパスワードを入力し、アカウントを認証する必要があります。 ただし Windows PowerShell Web Access 管理者は、クライアント証明書による認証をオプションで有効または無効にすることができます (<a href="https://technet.microsoft.com/en-us/library/hh831611(v=ws.11).aspx">Windows PowerShell Web Access のインストールと使用に関するページ</a>の「IIS マネージャーを使って既存の Web サイトにゲートウェイを構成するには」の手順 10. を参照)。 オプションのクライアント証明書機能は、Web サーバー (IIS) 構成の一環として、ユーザー名とパスワードに加えて有効なクライアント証明書の所有をエンド ユーザーに要求します。 クライアント証明書の層が有効になっている場合、Windows PowerShell Web Access のサインイン ページは、サインイン資格情報を評価する前に、有効な証明書の提示を求めるメッセージをユーザーに表示します。 クライアント証明書の認証によってクライアント証明書を自動的に確認します。</p>
<p>有効な証明書が見つからない場合、Windows PowerShell Web Access は通知を表示し、ここでユーザーは証明書を提示できます。 有効な証明書が見つかった場合、Windows PowerShell Web Access はサインイン ページを表示し、ここでユーザーは名前とパスワードを入力できます。</p>
<p>これが、Web サーバー (IIS) が提供する追加のセキュリティ設定の 1 つの例です。 IIS が提供するその他のセキュリティ機能の詳細については、「<a href="https://technet.microsoft.com/library/cc731278(ws.10).aspx">Configure Web Server Security (IIS 7) (Web サーバーのセキュリティを構成する (IIS 7))</a>」を参照してください。</p></td>
</tr>
<tr class="even">
<td><p>2</p></td>
<td><p>Windows PowerShell Web Access のフォーム ベースのゲートウェイ認証</p></td>
<td><p>Windows PowerShell Web Access のサインイン ページは、一連の資格情報 (ユーザー名とパスワード) を要求すると共に、対象コンピューターに対する別の資格情報を提示するためのオプションをユーザーに提供します。 ユーザーがその他の資格情報を提示しない場合、ゲートウェイへの接続に使われるプライマリのユーザー名とパスワードが、対象コンピューターへの接続にも使われます。</p>
<p>要求された資格情報は Windows PowerShell Web Access ゲートウェイで認証されます。 これらの資格情報は、ローカルの Windows PowerShell Web Access ゲートウェイ サーバーまたは Active Directory® の有効なユーザー アカウントである必要があります。</p>
<p>ゲートウェイでユーザーが認証されると、Windows PowerShell Web Access が承認規則を確認して、要求された対象コンピューターへのアクセスがユーザーに許可されているかどうかを検証します。 問題なく承認されると、ユーザーの資格情報が対象コンピューターに渡されます。</p></td>
</tr>
<tr class="odd">
<td><p>3</p></td>
<td><p>Windows PowerShell Web Access の承認規則</p></td>
<td><p>ゲートウェイでユーザーが認証されると、Windows PowerShell Web Access が承認規則を確認して、要求された対象コンピューターへのアクセスがユーザーに許可されているかどうかを検証します。 問題なく承認されると、ユーザーの資格情報が対象コンピューターに渡されます。</p>
<p>これらの規則の評価は常にゲートウェイでのユーザー認証の後で実行され、その後、対象コンピューターでユーザー認証を実行できるようになります。</p></td>
</tr>
<tr class="even">
<td><p>4</p></td>
<td><p>対象コンピューターでの認証と承認規則</p></td>
<td><p>Windows PowerShell Web Access におけるセキュリティの最後の層は、対象コンピューター独自のセキュリティ構成です。 Windows PowerShell Web Access 経由で対象コンピューターを操作する Windows PowerShell Web ベース コンソールを実行するには、ユーザーは、対象コンピューターと Windows PowerShell Web Access の承認規則でアクセス権を適切に構成する必要があります。</p>
<p>この層は、ユーザーが <strong>Enter-PSSession</strong> または <strong>New-PSSession</strong> コマンドレットを実行して Windows PowerShell から対象コンピューターへのリモート Windows PowerShell セッション作成を試みた場合に、試行された接続を評価するのと同じセキュリティ メカニズムを提供します。</p>
<p>既定では、Windows PowerShell Web Access は、ゲートウェイと対象コンピューター両方での認証に、プライマリのユーザー名とパスワードを使います。 Web ベースのサインイン ページでは、<strong>[オプションの接続設定]</strong> というセクションで、必要に応じて対象コンピューターに対する別の資格情報を提示するためのオプションをユーザーに提供しています。 ユーザーがその他の資格情報を提示しない場合、ゲートウェイへの接続に使われるプライマリのユーザー名とパスワードが、対象コンピューターへの接続にも使われます。</p>
<p>承認規則を使って、特定のセッション構成に対するユーザーのアクセスを許可することができます。 担当者は、Windows PowerShell Web Access 用の制限付き実行空間またはセッション構成を作成し、特定のユーザーが特定のセッション構成だけにアクセスできるようにして、それらのユーザーを Windows PowerShell Web Access にサインインさせることができます。 アクセス制御リスト (ACL) を使って特定のエンドポイントにアクセスできるユーザーを決定できることに加えて、このセクションで説明する承認規則を使用すると、エンドポイントへのアクセスを特定のユーザーにさらに制限することができます。 制限付き実行空間の詳細については、MSDN の<a href="https://msdn.microsoft.com/library/windows/desktop/ee706589.aspx">制約付き実行空間に関するページ</a>を参照してください。</p></td>
</tr>
</tbody>
</table>

<a href="" id="BKMK_configrules"></a>
###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">承認規則の構成</span></a>

------------------------------------------------------------------------

多くの場合、管理者が Windows PowerShell Web Access ユーザー用に必要とする承認規則は、環境内で Windows PowerShell のリモート管理用に定義されているものと同じになります。 このセクションの最初の手順では、安全な承認規則を追加することによって、サインインして 1 つのセッション構成で 1 つのコンピューターを管理するためのアクセスを、ユーザー 1 人に許可する方法を説明します。 2 つ目の手順では、不要になった承認規則を削除する方法について説明します。

カスタム セッション構成を使用することで、特定のユーザーに対して Windows PowerShell Web Access の制限付き実行空間内でのみ作業することを許可する場合、カスタム セッション構成は、これらが参照される承認規則を追加する前に作成してください。 Windows PowerShell Web Access コマンドレットを使用してカスタム セッション構成を作成することはできません。 カスタム セッション構成の作成方法の詳細については、MSDN サイトの「[about_Session_Configuration_Files](https://msdn.microsoft.com/library/windows/desktop/hh847838.aspx)」を参照してください。

Windows PowerShell Web Access コマンドレットは、ワイルドカード文字 、アスタリスク( \*) をサポートしています。 文字列内のワイルドカード文字はサポートされていません。アスタリスクは、プロパティ (ユーザー、コンピューター、セッション構成) ごとに 1 つ使うことができます。

<table>
<colgroup>
<col width="100%" />
</colgroup>
<thead>
<tr class="header">
<th><span><img src="https://i-technet.sec.s-msft.com/dynimg/IC101471.jpeg" title="System_CAPS_note" alt="System_CAPS_note" id="s-e6f6a65cf14f462597b64ac058dbe1d0-system-media-system-caps-note" /></span><span class="alertTitle">注意 </span></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>承認規則を使ってユーザーのアクセスを許可し Windows PowerShell Web Access 環境のセキュリティ保護を実現するその他のさまざまな方法については、このトピックの「<a href="#BKMK_others">承認規則のその他のシナリオの例</a>」を参照してください。</p></td>
</tr>
</tbody>
</table>

#### 制限的な承認規則を追加するには

1.  次のいずれかを実行して、管理者特権を使って Windows PowerShell セッションを開きます。

    -   Windows デスクトップで、タスク バーの **[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

    -   Windows の**スタート**画面で、**[Windows PowerShell]** を右クリックし、**[管理者として実行]** をクリックします。

2.  <span class="label">セッション構成を使用してユーザーのアクセスを制限するためのオプション手順:</span> 使用する場合、規則で既にセッション構成があることを確認します。 まだ作成されていない場合は、MSDN の「[about_Session_Configuration_Files](https://msdn.microsoft.com/library/windows/desktop/hh847838.aspx)」に記載されているセッション構成の作成手順を使用してください。

3.  次のように入力して **Enter** キーを押します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_1079478f-cd51-4d35-8022-4b532a9d57a4'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName <domain\user | computer\user> -ComputerName <computer_name> -ConfigurationName <session_configuration_name>

    この承認規則により、特定のユーザー 1 人が、通常アクセスが許可されているネットワーク上の 1 つのコンピューターにアクセスして、このユーザーが通常必要とするスクリプトとコマンドレットに制限された特定の 1 つのセッション構成にアクセスできるようになります。 次の例では、<span class="code">Contoso</span> ドメインの <span class="code">JSmith</span> というユーザーに、<span class="code">Contoso_214</span> というコンピューターを管理し、<span class="code">NewAdminsOnly</span> というセッション構成を使うためのアクセス権が付与されます。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_4e760377-e401-4ef4-988f-7a0aec1b2a90'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –UserName Contoso\JSmith -ComputerName Contoso_214 -ConfigurationName NewAdminsOnly

4.  いずれかを実行して、ルールが作成されたことを確認、 **Get-pswaauthorizationrule** コマンドレット、または **Test-pswaauthorizationrule UserName &lt;ドメイン \\ ユーザー | computer\\user&gt; -computername** &lt;computer_name&gt;します。 たとえば、 **Test-pswaauthorizationrule – UserName Contoso\\JSmith – ComputerName Contoso_214**します。

#### 承認規則を削除するには

1.  Windows PowerShell セッションが開かれていない場合は、このセクションの[非制限的な承認規則を追加する方法](#BKMK_arar)の手順 1. を参照してください。

2.  次のように入力して **Enter** キーを押します。*rule ID* には削除する規則の一意の ID 番号を指定します。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_0daef66d-0ecf-47fb-a8a0-d4dbceb8409d'); "Copy to clipboard.")

        Remove-PswaAuthorizationRule -ID <rule ID>

    または、削除する規則の ID 番号を知らないがフレンドリ名は知っている場合、規則の名前を取得し、パイプ処理によって名前を <span class="code">Remove-PswaAuthorizationRule</span> コマンドレットに渡すことによって、規則を削除できます。次の例を参照してください: Get-PswaAuthorizationRule -RuleName &lt;*ルール名*&gt; | Remove-PswaAuthorizationRule

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
    <td><p>指定した承認規則を削除するかどうかを確認するメッセージは表示されず、<strong>Enter</strong> キーを押すと規則が削除されます。 <strong>Remove-PswaAuthorizationRule</strong> コマンドレットを実行する前に、削除する承認規則が正しいことを必ず確認してください。</p></td>
    </tr>
    </tbody>
    </table>

<a href="" id="BKMK_others"></a>
####

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">その他の承認規則のシナリオ例</span></a>

------------------------------------------------------------------------

あらゆる Windows PowerShell セッションはいずれかのセッション構成を使います。セッションにセッション構成が指定されていない場合、Windows PowerShell は、組み込み済みの Windows PowerShell セッション構成である Microsoft.PowerShell という既定値を使います。 既定のセッション構成には、コンピューターで使うことができるすべてのコマンドレットが含まれています。 管理者は、制限付き実行空間 (エンド ユーザーが実行できるコマンドレットとタスクの範囲が制限されている) が指定されたセッション構成を定義することで、すべてのコンピューターに対するアクセスを制限できます。 1 つのコンピューターに対する全言語でのアクセス、または Windows PowerShell リモート管理コマンドレットだけに対するアクセスを許可されているユーザーは、最初の対象コンピューターに接続されているその他のコンピューターに接続できます。 制限付き実行空間を定義することで、許可されている Windows PowerShell 実行空間から他のコンピューターへのユーザー アクセスを阻止し、Windows PowerShell Web Access 環境のセキュリティを強化できます。 セッション構成は、Windows PowerShell Web Access からアクセス可能な状態にすると管理者が判断したすべてのコンピューターに (グループ ポリシーを使って) 配布できます。 セッション構成の詳細については、「[about_Session_Configurations](https://technet.microsoft.com/library/dd819508.aspx)」を参照してください。 次に、シナリオの例をいくつか示します。

-   管理者が、制限付き実行空間を指定して、**PswaEndpoint** というエンドポイントを作成します。 その後管理者が **\*,\*,PswaEndpoint** という規則を作成し、このエンドポイントをその他のコンピューターに配布します。 この規則によって、エンドポイント **PswaEndpoint** を持つすべてのコンピューターにすべてのユーザーがアクセスできるようになります。 規則セットにこの承認規則だけ定義されている場合、このエンドポイントを持たないコンピューターにはアクセスできません。

-   制限付き実行空間を指定して **PswaEndpoint** というエンドポイントを作成した後は、アクセスを特定のユーザーに制限します。 管理者は **Level1Support** というユーザー グループを作成し、**Level1Support,\*,PswaEndpoint** という規則を定義します。 この規則によって、**Level1Support** グループのすべてのユーザーが、**PswaEndpoint** 構成を持つコンピューターにアクセスできるようになります。 同様に、アクセスを特定のコンピューターに制限することも可能です。

-   管理者は、その他のユーザーよりも権限の強いアクセスを特定のユーザーに許可することができます。 たとえば、管理者が、**Admins** と **BasicSupport** という 2 つのユーザー グループを作成します。 管理者はさらに、制限付き実行空間を指定して **PswaEndpoint** というエンドポイントを作成し、**Admins,\*,\*** と **BasicSupport,\*,PswaEndpoint** という 2 つの規則を定義します。 最初の規則は、**Admin** グループのすべてのユーザーにすべてのコンピューターへのアクセスを許可します。2 つ目の規則は、**BasicSupport** グループに、**PswaEndpoint** を持つコンピューターだけに対するアクセスを許可します。

-   管理者がプライベートなテスト環境をセットアップして、すべての承認済みネットワーク ユーザーに、通常アクセスが許可されるネットワーク内のすべてのコンピューターに対するアクセスと、通常アクセスが許可されるすべてのセッション構成に対するアクセスを許可します。 これはプライベートなテスト環境であるため、管理者が作成する承認規則は安全ではありません。 管理者は <span class="code">Add-PswaAuthorizationRule \* \* \*</span> コマンドレットを実行します。ここで使われているワイルドカード文字 **\*** は、すべてのコンピューター、すべてのユーザー、すべてのセッション構成をそれぞれ示します。 このルールは、以下のと同じ: <span class="code">Add-pswaauthorizationrule – UserName \ * - ComputerName \ * - ConfigurationName \ *</span>します。

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
    <td><p>この規則は、Windows PowerShell Web Access が提供する承認規則によるセキュリティ層をバイパスするものであり、セキュリティ保護された環境には推奨されません。</p></td>
    </tr>
    </tbody>
    </table>

-   管理者は、ワークグループとドメインの両方が含まれている環境で、対象コンピューターへの接続をユーザーに許可する必要があります。ワークグループ コンピューターは、ドメイン内の対象コンピューターに接続するために使用されることがあります。ドメイン内のコンピューターは、ワークグループ内の対象コンピューターに接続するために使用されることがあります。 ワークグループ内にはゲートウェイ サーバー *PswaServer* があり、ドメイン内には対象コンピューター *srv1.contoso.com* があります。 ユーザー *Chris* は、ワークグループ ゲートウェイ サーバーと対象コンピューターの両方で承認されているローカル ユーザーです。 ワークグループ サーバー上のユーザー名は *chrisLocal*、対象コンピューター上のユーザー名は *contoso\\chris* です。 Chris に srv1.contoso.com へのアクセスを承認するには、管理者は以下の規則を追加する必要があります。

    [Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_8d183d3d-1c19-44b8-9297-530b0efc7c79'); "Copy to clipboard.")

        Add-PswaAuthorizationRule –userName PswaServer\chrisLocal –computerName srv1.contoso.com –configurationName Microsoft.PowerShell

    この規則例では、Chris がゲートウェイ サーバーで認証され、Chris による *srv1* へのアクセスが承認されます。 サインイン ページで、Chris は 2 番目の資格情報 (*contoso\\chris*) を **[オプションの接続設定]** に指定する必要があります。 ゲートウェイ サーバーでは、対象コンピューター (*srv1.contoso.com*) で Chris を認証するために、追加の資格情報が使用されます。

    このシナリオでは、Windows PowerShell Web Access が対象コンピューターへの接続に成功するのは、次の動作に成功し、1 つ以上の承認規則で許可された場合のみです。

    1.  *server_name*\\*user_name* の形式のユーザー名を承認規則に追加することによる、ワークグループ ゲートウェイ サーバーでの認証

    2.  サインイン ページの **[オプションの接続設定]** で指定された別の資格情報を使用することによる、対象コンピューターでの認証

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
    <td><p>ゲートウェイ コンピューターと対象コンピューターが別々のワークグループまたはドメインにある場合は、2 つのワークグループ コンピューター間、2 つのドメイン間、またはワークグループとドメインの間で、信頼関係を確立する必要があります。 Windows PowerShell Web Access の承認規則コマンドレットを使用してこの関係を構成することはできません。 承認規則では、コンピューター間の信頼関係は定義されません。承認規則では、特定の対象コンピューターとセッション構成に接続できるようにユーザーを承認することしかできません。 異なるドメイン間で信頼関係を構成する方法の詳細については、<a href="https://technet.microsoft.com/library/cc794775.aspx">ドメインおよびフォレストの信頼の作成に関するページ</a>を参照してください。 信頼されたホストの一覧にワークグループ コンピューターを追加する方法の詳細については、「<a href="https://technet.microsoft.com/library/dd759202.aspx">サーバー マネージャーによるリモート管理</a>」を参照してください。</p></td>
    </tr>
    </tbody>
    </table>

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">複数のサイトの承認規則の 1 つのセットを使用します。</span></a>

------------------------------------------------------------------------

承認規則は 1 つの XML ファイルに格納されます。 既定では、この XML ファイルのパス名は %windir%\\Web\\PowershellWebAccess\\data\\AuthorizationRules.xml. です。

承認規則 XML ファイルのパスは、**powwa.config** ファイルに格納されます。このファイルは %windir%\\Web\\PowershellWebAccess\\data にあります。 管理者は、**powwa.config** に記述されている既定パスへの参照を、設定や要件に合わせて柔軟に変更することができます。 管理者がファイルの場所を変更できるので、構成上の必要に応じて、複数の Windows PowerShell Web Access ゲートウェイが同じ承認規則を使うことができるようになります。

<a href="" id="BKMK_sesmgmt"></a>

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">セッション管理</span></a>
<a href="/en-us/library/dn282394(v=ws.11).aspx#Anchor_1" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

Windows PowerShell Web Access では、ユーザーが同時に接続できるセッションは既定で 3 つまでに制限されています。 Web アプリケーションの **web.config** ファイルを IIS マネージャーで編集することで、ユーザーごとにサポートされるセッションの数を変更できます。 **web.config** ファイルのパスは $Env:Windir\\Web\\PowerShellWebAccess\\wwwroot\\Web.config です。

何らかの設定が編集された場合、Web サーバーの既定の構成によってアプリケーション プールが再起動します。 たとえば **web.config** ファイルが編集された場合、アプリケーション プールが再起動します。 Windows PowerShell Web Access はインメモリ セッション状態を使うため、アプリケーション プールの再起動によって、Windows PowerShell Web Access セッションにサインインしているユーザーのセッションは切断されます。

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">サインイン ページの既定のパラメーターを設定</span></a>

------------------------------------------------------------------------

Windows PowerShell Web Access ゲートウェイを Windows Server 2012 R2 で実行する場合、Windows PowerShell Web Access サインイン ページに表示される設定に既定の値を構成できます。 前の段落で説明されている **web.config** ファイルに値を構成できます。 サインイン ページの設定の既定値は、web.config ファイルの **appSettings** セクションにあります。**appSettings** セクションの例を次に示します。 これらの設定の多くに有効な値は、Windows PowerShell の [New-PSSession](https://technet.microsoft.com/library/hh849717.aspx) コマンドレットの対応するパラメーターのものと同じです。 たとえば、次のコード ブロックに示された <span class="code">defaultApplicationName</span> キーは、ターゲット コンピューター上の **$PSSessionApplicationName** ユーザー設定変数の値になります。

[Copy](javascript:if%20(window.epx.codeSnippet)window.epx.codeSnippet.copyCode('CodeSnippetContainerCode_6ccfd0a1-485a-4ac5-9636-89ebab501bef'); "Copy to clipboard.")

    <appSettings>
            <add key="maxSessionsAllowedPerUser" value="3"/>
            <add key="defaultPortNumber" value="5985"/>
            <add key="defaultSSLPortNumber" value="5986"/>
            <add key="defaultApplicationName" value="WSMAN"/>
            <add key="defaultUseSslSelection" value="0"/>
            <add key="defaultAuthenticationType" value="0"/>
            <add key="defaultAllowRedirection" value="0"/>
            <add key="defaultConfigurationName" value="Microsoft.PowerShell"/>
    </appSettings>

###

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">タイムアウトと計画されていない接続の切断</span></a>

------------------------------------------------------------------------

Windows PowerShell Web Access セッションではタイムアウトが発生します。 Windows Server 2012 で実行する Windows PowerShell Web Access では、非アクティブな状態が 15 分続くと、セッションのタイムアウト メッセージがサインインしているユーザーに対して表示されます。 タイムアウト メッセージが表示されてから 5 分以内にユーザーの応答がない場合、セッションが終了しユーザーがサインアウトされます。 セッションのタイムアウト時間は、IIS マネージャーの Web サイト設定で変更できます。

Windows Server 2012 R2 で実行する Windows PowerShell Web Access では、非アクティブな状態が 20 分続くと、セッションは既定でタイムアウトします。 ユーザー自身がセッションを閉じたためではなく、ネットワーク エラーまたはその他の予期しないシャットダウンや障害のために、ユーザーがセッションから切断された場合、クライアント側でタイムアウト期間が経過するまで、Windows PowerShell Web Access セッションは実行を続け、ターゲット コンピューターに接続されたままになります。 既定の 20 分またはゲートウェイ管理者が指定したタイムアウト期間のどちらか短い方が経過すると、セッションは切断されます。

ゲートウェイ サーバーが Windows Server 2012 R2 を実行している場合の Windows PowerShell Web Access では、ユーザーは保存されているセッションに再接続できますが、ネットワーク エラー、予期しないシャットダウン、または他のエラーによるセッションの切断が発生した場合、ユーザーはゲートウェイ管理者によって指定されたタイムアウト期間が経過するまでは、保存されているセッションを表示または再接続できません。

<a href="javascript:void(0)" class="LW_CollapsibleArea_TitleAhref" title="Collapse"><span class="cl_CollapsibleArea_expanding LW_CollapsibleArea_Img"></span><span class="LW_CollapsibleArea_Title">関連項目</span></a>
<a href="/en-us/library/dn282394(v=ws.11).aspx#Anchor_2" class="LW_CollapsibleArea_Anchor_Img" title="Right-click to copy and share the link for this section"></a>

------------------------------------------------------------------------

[Windows PowerShell Web Access のインストールと使用](https://technet.microsoft.com/en-us/library/hh831611(v=ws.11).aspx)
[about_Session_Configurations](https://technet.microsoft.com/library/dd819508.aspx)
[Windows PowerShell Web Access コマンドレット](https://technet.microsoft.com/library/hh918342.aspx)

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


