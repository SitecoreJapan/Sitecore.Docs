##########################################
Sitecoreの認証動作の変化を理解する
##########################################

Sitecore Identity (SI) は、Sitecore 9.0 で導入された連携認証機能を使用しています。これらの機能は、OWIN 認証ミドルウェアをベースにしています。ただし、Sitecore 9.0では、OWIN認証の統合と連携認証はデフォルトで無効になっています。

Sitecore 9.1 以降では、Sitecore Identity はデフォルトで有効になっています。OWIN認証と連携認証は、SIで必要とされているため、有効になっています。

このトピックでは、Sitecore認証の動作の変更点と、その方法の概要について説明します。

********************
認証範囲の設定
********************

デフォルトでは、SitecoreはSIサーバーのプロバイダがSitecoreクライアントサイトの認証を処理するように設定されています。これは、SIサーバを経由してシェルで認証した場合、Webサイトはそのユーザーを受け入れず、Webサイト内では匿名となることを意味します。また、GetSignInUrlInfoPipelineパイプラインを使用してWebサイト上でサインインリンクを生成した場合、SIサーバでサインインするためのログインリンクが不意にそこに表示されないことを意味します。

認証スコープを設定するには

* sitecore/federatedAuthentication/identityProvidersPerSites/ ノードの下にある mapEntry ノードで、許可したいサイトと ID プロバイダの組み合わせを指定します。

Sitecore には、定義済みのサイトリストを持ついくつかの mapEntry ノードが用意されています。すべてのノードは、意味のある値を持つ name 属性を持っています。

* すべてのサイト
* ウェブデータベースを持つサイト
* コアと不特定多数のデータベースを持つサイト
* エクストラネットドメインを持つサイト
* 管理者サイト

.. note:: これらの定義済みの mapEntry ノードは動的に作成され、独自の mapEntry の mapEntry/sites セクションで特別な式を使用できることを示しています。詳細は Config.Authentication.Owin.Authentication.config ファイルを確認してください。

デフォルトでは、SI サーバー プロバイダーは、コアと未指定のデータベース mapEntry ノードを持つサイトに配置されます。

****************************************************
新しいログインページURLでSitecoreにアクセスする
****************************************************

SI以前は、/sitecore/loginと/sitecore/admin/login.aspx URLを使用して、それぞれシェルサイトと管理サイトにログインしていました。これらのURLは、Sitecore Identityでは使用されません。

Sitecoreにアクセスするには、/sitecoreまたは/sitecore/admin URLを使用し、ログアウトボタンを使用してサインアウトしたり、別のユーザーに変更したりすることをお勧めします。

Sitecore Federated Authenticationは、新しいログインページエンドポイントを提供します。これにより、Sitecoreはユーザーを外部のIDプロバイダのログインページに直接リダイレクトし（Sitecoreにログインページを表示せずに）、ユーザーが対応するボタンをクリックするまで待ちます。

この新しいログインエンドポイントのURLは、次のような形式になっています。

* (loginPath) は構成変数です ($(identityProcessingPathPrefix)login = /identity/login)。
* {site_name}は、loginPage属性値が設定されているサイトノードの名前属性値です。
* identity_provider} は、ユーザーをリダイレクトさせたいログイン・ページの ID プロバイダの名前です。
* inner_identity_provider} はオプションです。 これは identity_provider 内の内部プロバイダの名前です。この値を設定すると、ユーザーは直ちに inner_identity_provider ログイン・ページに直接リダイレクトされます。

.. note:: inner_identity_provider identity provider は、identity_provider identity provider に acr_value = idp:inner_identity_provider として送信される。そのため、identity_provider identity provider は acr_value をサポートしなければなりません。

SIサーバープロバイダはSitecoreのSitecoreIdentityServer名で設定されており、Sitecore.Owin.Authentication.IdentityServer.configファイルには以下のように記述されています。

.. code-block:: xml

    <sites>
    <site name="shell" set:loginPage="$(loginPath)shell/SitecoreIdentityServer"/>
    <site name="admin" set:loginPage="$(loginPath)admin/SitecoreIdentityServer"/>
    </sites>

.. note:: 

    クロスオリジンの問題を防ぐために、サイトの loginPage 属性値に相対 URL が含まれていることを確認する必要があります。

    identity/login/... エンドポイントは、内部的に GetSignInUrlInfoPipeline パイプライン・パイプラインを使用して、選択した外部プロバイダへの適切なサインイン・リンクを生成し、必要なすべてのデータをそこに渡します。このログイン・ページ・フォーマットは、サイト・ノードの loginPage 属性にのみ使用し、GetSignInUrlInfoPipeline パイプライン・パイプラインは、プレゼンテーション・レイヤーの特定のサイトの外部サインイン URL を取得するために使用します。


************************************
sitecore/login ページを取得
************************************

Sitecore 9.0では、以前に設定した外部IDプロバイダのログインボタンを復活させることができます。

Sitecore.Owin.Authentication.IdentityServer.config設定ファイルは、シェルサイトとadminサイトのloginPage属性を、Sitecoreが扱う新しい特別なエンドポイントにパッチします。

SI が有効なときに /sitecore/login ページにアクセスしようとすると、シェルサイトで指定されたログインページにリダイレクトされます。

.. note:: この動作は、InterceptLegacyShellLoginPageプロセッサが担当しています。

SI サーバーにアクセスできず、Sitecore が初期メタデータを取得できない場合、ユーザーは /sitecore/login?fbc=1 ページで終了します。

.. note:: IdentityProviderIsInaccessibleプロセッサとその構成を確認してください。

連携認証を使用してシェルでユーザーにサインインするように外部の ID プロバイダをすでに構成している場合は、SI サーバーのログインページにはこれらの余分なログインボタンが表示されないため、/sitecore/ログインページを使用する必要があります。

.. note:: SIサーバーに外部のIDプロバイダを追加する場合は、「Federation Gateway」を参照してください。

Sitecore がユーザーを sitecore/login ページからリダイレクトさせないようにするには。

1. シェルログインページを /sitecore/login に戻すか、/sitecore/login に URL パラメータ (?fbc=1) を追加してリクエストします。
2. または、InterceptLegacyShellLoginPage プロセッサの legacyShellLoginPage プロパティをランダムな値にパッチします。

これらのアクションのいずれかを実行することで、Sitecore がユーザーを /sitecore/login ページからリダイレクトさせないようにします。SIサーバーは、Sitecoreの通常の外部IDプロバイダとして設定されており、/sitecore/loginページにサインインボタンが表示されていることを意味します。キャプションは「Go to login」です。

************************************
Sitecore Identityを無効にする
************************************

Sitecore インスタンスは SI クライアントですが、9.1 以前のバージョンでは SI サーバーがなくても Sitecore が動作するように、SI を無効にすることができます。

デフォルトでは、Sitecore が起動した後の最初のサインイン時に、Sitecore インスタンスが SI サーバーに到達できない場合、/sitecore/login ページをログインページのフォールバックとして使用します。

.. note:: 認証フォールバックが発生しても、OWIN.Authentication.Enabled設定で有効になっているため、OWIN認証ミドルウェアが使用されます。

SIサーバーを全く使用しない場合。

* この設定ファイルを有効にします。
   \App_Config\IncludeExamples\Sitecore.Owin.Authentication.IdentityServer.Disabler.config.example. このファイルは、以下のことを行う。

    * SitecoreIdentityServer プロバイダの Enabled プロパティを false に設定します。

    .. note:: sitecore/federatedAuthentication/identityProviders で設定されたすべての外部 ID プロバイダには、個々の ID プロバイダが Sitecore に登録されないようにするために使用する Enabled プロパティがあります。

    * シェルサイトと管理サイトのloginPage属性を初期値（/sitecore/loginと/sitecore/admin/login.aspx）にパッチします。

OWIN と連携認証を無効にするには、この設定ファイルを有効にします。

* この設定ファイルを有効にします。
  \App_Config\IncludeExamples\Sitecore.Owin.Authentication.Disabler.config.example. このファイルは、以下のことを行います。

    * Owin.Authentication.Enabled および FederatedAuthentication.Enabled を false に設定します。
    * シェルサイトと管理サイトの loginPage 属性を初期値（/sitecore/login と /sitecore/admin/login.aspx）にパッチします。

* この設定を web.config ファイルに追加します。

.. code-block:: xml

    <add key="owin:AutomaticAppStartup" value="false" />

web.configファイル内の元のauthenticatiomノードを復元します。

.. code-block:: xml

    <authentication mode="Forms">
        <forms name=".ASPXAUTH" cookieless="UseCookies" />
    </authentication>

*********************************
ユーザーサインアウトの実装
*********************************

Sitecore 9.1では、フェデレート認証が拡張されました。ユーザーがSitecoreからサインアウトする際に、外部のIDプロバイダからのサインアウトをより簡単に実装できるようになりました。

ユーザーが外部の ID プロバイダからサインアウトすると、Sitecore Identity はユーザーをこの ID プロバイダのログアウトページにリダイレクトし、その後 Sitecore に戻ります。通常、Sitecore と基礎となる ID プロバイダの両方から完全なサインアウトを 1 回のリクエストで行うことはできない。しかし、AuthenticationManager.Logout() メソッドを使用すると、Sitecore Identity がすべてを自動的に処理する。

この機能は、SI サーバープロバイダ（構成では SitecoreIdentityServer）に対してのみデフォルトでオンになっています。sitecore/federatedAuthentication/identityProviders/identityProvider[id=SitecoreIdentityServer]/triggerExternalSignOut は、デフォルトでは true になっています。

.. important:: この機能は、認証ミドルウェアの ID プロバイダに対して postLogoutRedirectUri を正しく構成し、ID プロバイダ自身に対して postLogoutRedirectUri を許可する必要があります。Sitecore は、外部サインアウトが発生したことを確実にするために、これに依存しています。postLogoutRedirectUriを正しく設定しないと、ユーザーはサインアウト後にSitecoreにアクセスしようとするたびに、外部プロバイダのサインアウトページにリダイレクトされます。ユーザーはこれを避けるために、1分待つか、Sitecoreのクッキーをクリーンアップすることができます。

*********************************
ユーザーロックアウトの設定
*********************************

ユーザーアカウントのロックアウトは、ブルートフォース攻撃として知られるパスワード推測攻撃を回避するのに役立ちます。ブルートフォース攻撃とは、文字、数字、記号の可能な組み合わせをすべて系統的に試して、正しい組み合わせが見つかるまでパスワードを発見しようとする攻撃です。

ユーザー アカウントのロックアウトを設定するには

1. MaxInvalidPasswordAttempts と PasswordAttemptWindow を、Sitecore:IdentityServer:SitecoreMembershipOptions:MaxInvalidPasswordAttempts と Sitecore:IdentityServer:SitecoreMembershipOptions:PasswordAttemptWindow の設定で構成します。

2. または、Sitecore インスタンスの Web.config ファイルで、MaxInvalidPasswordAttempts と PasswordAttemptWindow を指定します。これは、Sitecore Identityサーバーが無効になっているか、identityServer.xmlのパスワードポリシーパラメータが指定されていない場合にのみ機能します。

.. important:: SI サーバールート https://{si_server}/ と https://{si_server}/account/login URL へのアクセスを組織外で制限する必要があります。

************************************
認証クッキーの有効期限を指定する
************************************

クッキーには2種類あります。

* セッションクッキー（非永続的） - これらは一時的なクッキーファイルです。ブラウザを閉じると消去されます。
* 永続的クッキー - あなたが手動で削除するか、ブラウザが削除するまで、ブラウザはこれらのクッキーファイルを保存し、永続的クッキーファイル自体に指定された寿命に基づいて削除します。

最近のブラウザは、適切なブラウザオプションがオンになっていると、ブラウザセッション間でセッションクッキーを保存する傾向があります。これにより、セッションクッキーが永続的なものと同じように動作することがよくあります。OWIN認証では、クッキーの寿命値をクッキー値自体に保存することができます。つまり、クッキーの有効期限が切れていても、ブラウザはサーバにクッキーを送信しますが、ウェブアプリケーションはクッキーを有効期限切れとして扱います。

認証クッキーの有効期限を指定するには

* 以下のパッチスニペットを使用して、デフォルトのクッキーの有効期限を指定したり、有効期限のスライドを有効にしたり無効にしたりします。

    .. code-block:: xml

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <sitecore>
            <pipelines>
            <owin.initialize>
                <processor name="CookieAuthentication">
                <!-- Controls how much time the cookie will remain valid from the point it is created.
                    Format: d:hh:mm:ss

                    No matter the cookie is persistent or not, OWIN authentication will not pass the expired cookie.
                    ExpireTimeSpan could be overwritten by the http://www.sitecore.net/identity/claims/cookieExp claim where
                    the claim value is Unix time expressed as the number of seconds that have elapsed since 1970-01-01T00:00:00Z. -->
                <ExpireTimeSpan>00:30:00</ExpireTimeSpan>
                <SlidingExpiration>true</SlidingExpiration>
                </processor>
                ...

Web アプリケーションは、ユーザーが [記憶する] オプションを選択すると、永続的な認証クッキーを作成します。統合認証による認証では、永続的でないクッキーのみが生成されます。

たとえば、外部 ID プロバイダで Remember me オプションを選択せずに外部 ID プロバイダを介してサインインした場合、ブラウザ セッションが終了した後に再度サインインする必要があります。

外部の ID プロバイダを経由してサインインした場合、そのプロバイダで Remember me オプションを選択すると、ブラウザセッションの有効期限が切れると Sitecore 認証クッキーが失われます。 しかし、IDプロバイダに素早く自動リダイレクトして戻ってくると、再び自動的にSitecoreにサインインしたことになります。

特定のIDプロバイダのCookie ExpireTimeSpan設定をオーバーライドするには、以下の手順に従います。

* 1970-01-01T0:0:0Z から日付/時刻までの UTC で測定した秒数を指定する値で http://www.sitecore.net/identity/claims/cookieExp クレームを追加する ID プロバイダのクレーム変換を指定します。

この値は、ブラウザによる処理のために認証クッキーを受け入れてはならない時間を示します。

Sitecore は、この目的のために Sitecore Identity サーバープロバイダの exp クレーム値を使用する。

.. code-block:: xml

    <sitecore>
    <federatedAuthentication>
        <identityProviders>
        <identityProvider id="SitecoreIdentityServer"  ...>
            <transformations ...>
                <!-- owin.cookieAuthentication.signIn pipeline uses http://www.sitecore.net/identity/claims/cookieExp claim to override authentication cookie expiration.
                    'exp' claim value can be configured on Sitecore Identity server on the client configuration by IdentityTokenLifetimeInSeconds setting.
                    Note: Claim value is Unix time expressed as the number of seconds that have elapsed since 1970-01-01T00:00:00Z -->
                <transformation name="use exp claim for authentication cookie expiration" type="Sitecore.Owin.Authentication.Services.DefaultTransformation, Sitecore.Owin.Authentication">
                <sources hint="raw:AddSource">
                    <claim name="exp"/>
                </sources>
                <targets hint="raw:AddTarget">
                    <claim name="http://www.sitecore.net/identity/claims/cookieExp"/>
                </targets>
                <keepSource>true</keepSource>
                </transformation>
                ...

.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/understanding-sitecore-authentication-behavior-changes.html
