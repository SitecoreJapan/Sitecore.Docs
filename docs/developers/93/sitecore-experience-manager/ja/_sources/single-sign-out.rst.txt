##########################################
シングルサインアウト
##########################################

認証クッキーを削除することで、ユーザーを Sitecore Identity サーバー (SI サーバー) からサインアウトする。ただし、完全な統合サインアウトは、クライアントアプリケーション (およびおそらくアップストリームの ID プロバイダ) からユーザーをサインアウトすることも考慮する必要があることを意味する。

************************************************
ユーザーがサインアウトするとクライアントに通知
************************************************

SI サーバは、サーバサイドクライアント (MVC アプリケーションなど) のフロントチャネル仕様をサポートしています。これを使用して、クライアント・アプリケーションにユーザーがサインアウトしたことを確実に知らせることができます。

フロントチャネル仕様を介してサーバサイドのクライアントアプリケーションからユーザをサインアウトするには、SI サーバのサインアウトページは、ユーザがサインアウトしたことをクライアントに通知するための <iframe> をレンダリングする必要があります。通知したいクライアントは、コンフィグレーションで指定したFrontChannelLogoutUrisの設定値を持っている必要があります。

設定では、サインアウトのエンドポイント URI を定義します。複数のサブノードを使用して複数の URI を定義したり、ノード内で URI を区切るために | 記号を使用したりできます。また、{AllowedCorsOrigin}テンプレートを使用して構成を簡素化することもできます。このテンプレートを使用すると、すべてのオリジン（ホスト）にサインアウトが通知されます。

サインアウトのエンドポイントをオンまたはオフにするには、FrontChannelLogoutSessionRequired 設定を使用します。

これは設定の例です。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <Settings>
    <Sitecore>
        <IdentityServer>
        <Clients>
            <DefaultClient>
            <FrontChannelLogoutUris>
                <DefaultFrontChannelLogoutUri>{AllowedCorsOrigin}/sitecore/shell/FrontChannelLogout|https://service/extraLogoutEndpoint</DefaultFrontChannelLogoutUri>
            </FrontChannelLogoutUris>
            <FrontChannelLogoutSessionRequired>true</FrontChannelLogoutSessionRequired>
            </DefaultClient>
        </Clients>
        </IdentityServer>
    </Sitecore>
    </Settings>

SI サーバは、ユーザがどのクライアントにサインインしたかを追跡し、IIdentityServerInteractionService サービス上で API GetLogoutContextAsync を提供します。この API は、サインアウトページを <iframe> にレンダリングしなければならない SignOutIFrameUrl プロパティを持つ LogoutRequest オブジェクトを返します。

************************************************
クライアントのサインアウトエンドポイント
************************************************

サインアウト処理を行うためには、クライアントはSIサーバからのリクエストを処理するエンドポイントを実装する必要があります。Sitecoreインスタンスは、<Sitecoreインスタンスホスト名>/sitecore/shell/FrontChannelLogoutエンドポイントに実装されています。

************************************************************
Sitecoreホストアプリケーションのサインアウトエンドポイント
************************************************************

Sitecore.Plugin.Authentication.OpenIdConnectプラグインには、サインアウトエンドポイントが含まれています。デフォルトでは、<アプリケーションホスト名>/sitecore/shell/FrontChannelLogoutとなっていますが、設定で別のアドレスを指定することができます。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>

    <Settings>
    <Sitecore>
        <Authentication>
        <OpenIdConnectOptions>
            <FrontChannelLogoutEndpoint>/sitecore/shell/FrontChannelLogout</FrontChannelLogoutEndpoint>
        </OpenIdConnectOptions>
        </Authentication>
    </Sitecore>
    </Settings>



.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/single-sign-out.html