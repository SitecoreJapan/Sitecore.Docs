#########################################################################
SitecoreインスタンスとSitecore Identityサーバーを設定する
#########################################################################

Sitecore Identity (SI) サーバー認証を使用するために Sitecore インスタンスを構成するには、以下の手順を実行する必要があります。

**************************************
Sitecoreインスタンスを設定する
**************************************

Sitecore インスタンスを設定するには

1. 以下の内容で、SIサーバー認証を持つすべてのSitecoreインスタンスを有効にします。

    * SIサーバーの絶対URL（OpenId Connect用語ではAuthority）。これは、設定変数 $(identityServerAuthority) で設定します。これは展開プロセスで指定されます。
    * 登録されているクライアントの ID です。Sitecoreには、SIサーバーにID Sitecoreというデフォルトのクライアントが設定されています。SitecoreはこのIDを
      FederatedAuthentication.IdentityServer.ClientId 設定。
    * カスタムResource Owner Passwordフロー用の専用クライアントのID。Sitecore は、内部目的のためにカスタム Resource Owner パスワードフローを使用する。FederatedAuthentication.IdentityServer.ResourceOwnerClientId 設定は、このクライアントの ID を指定する。デフォルト値は SitecorePassword です。

2. Sitecoreインスタンス内のsitecoreidentity.secret接続文字列に格納するクライアントシークレットを設定し、ここにPasswordClientクライアントのシークレットリストでSIサーバーで表現されるクライアントシークレットを設定します。Sitecore:IdentityServer:Client:PasswordClient:ClientSecrets:...

Sitecoreは、連携認証の設定に従ってSIサーバに接続します。

**************************************
Sitecore Identityサーバーを設定する
**************************************

SI サーバーには、そのすべてのクライアントの構成が含まれている必要があります（IdentityServer4 クライアントを参照）。

Sitecore Identityサーバーを構成するには

1. Sitecore:IdentityServer:Client セクションを使用してクライアントを構成するか、依存性注入を使用する。
   各クライアント構成ノードには、IdentityServer4.Models.Client クラスのプロパティにバインドされた多数のプロパ ティが含まれています。ほとんどの場合、クラスプロパティと構成プロパティの名前が一致します。または、依存性注入を使用して、IdentityServer4 オプションの全セットにアクセスすることもできます。

   .. note:: Sitecore (Sitecore:IdentityServer:Client:DefaultClient)という定義済みのクライアントがある。

2. デフォルトの Sitecore クライアント宣言を再利用するには、許可されている RedirectUris、PostLogoutRedirectUris、および AllowedCorsOrigins 値のリストを拡張して、アプリケーションに適切な値を含める。
   以下の例のように、RedirectUris リストと PostLogoutRedirectUris リストで {AllowedCorsOrigin} スペシャルトークンを使用できます。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <Settings>
    <Sitecore>
        <IdentityServer>
        <Clients>
            <DefaultClient>
            ...
            <RedirectUris>
                <RedirectUri1>{AllowedCorsOrigin}/identity/signin</RedirectUri1>
                <RedirectUri2>{AllowedCorsOrigin}/signin-oidc</RedirectUri2>
            </RedirectUris>
            <PostLogoutRedirectUris>
                <PostLogoutRedirectUri1>{AllowedCorsOrigin}/identity/postexternallogout</PostLogoutRedirectUri1>
                <PostLogoutRedirectUri2>{AllowedCorsOrigin}/signout-callback-oidc</PostLogoutRedirectUri2>
            </PostLogoutRedirectUris>
            ...


3. AllowedCorsOrigins セクションで URL の protocol+domain+port 部分のみを指定するには、{AllowedCorsOrigin} トークンを使用します。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <Settings>
    <Sitecore>
        <IdentityServer>
        <Clients>
            <DefaultClient>
            ...
            <AllowedCorsOrigins>
                <AllowedCorsOriginsGroup1>https://host1|http://host1</AllowedCorsOriginsGroup1>
                <AllowedCorsOriginsGroup2>https://host2</AllowedCorsOriginsGroup2>
                <AllowedCorsOriginsGroup3>https://host3</AllowedCorsOriginsGroup3>
            </AllowedCorsOrigins>
            ...

Sitecoreは、RedirectUri*とPostLogoutRedirectUri*のノード値を{AllowedCorsOrigin}トークンで展開し、AllowedCorsOriginsリストで指定されたすべてのオリジンに対して許可されるようにします。


.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/configure-a-sitecore-instance-and-sitecore-identity-server.html