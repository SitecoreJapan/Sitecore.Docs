##########################################
Sitecore Identity
##########################################

Sitecore Identity（SI）とは、Sitecoreにログインするための仕組みです。これはSitecore 9.1で導入されました。これは、Sitecore 9.0 で導入された Federated Authentication 機能と、`IdentityServer4 <https://github.com/IdentityServer/IdentityServer4>`_ をベースにした Sitecore Identity サーバーをベースにしています。独立したアイデンティティプロバイダを提供し、Sitecoreのサービスやアプリケーション間でSSO（シングルサインオン）を設定することができます。

フロントエンドのログイン（コンテンツ配信サーバー上）にはFederated Authenticatiionを使用することができますが、すべてのSitecore（バックエンド）認証には常にSitecore Identityを使用することをお勧めします。

.. note:: Sitecore 9.1.0以降はActive Directoryモジュールをサポートしていないため、代わりにfederated認証を使用する必要があります。

Sitecore Identityを使用する場合、サインインの流れは次のようになります。

* あなたはSitecoreの認可されたユーザーです。
  次にアクセス権があります。
* あなたはSitecoreで認証されていません。
  その後、SIサーバーにリダイレクトされます。

    * まだSIサーバーで認証されていない場合。
      その後、SIサーバーのログインページでサインイン資格情報を入力するように促されます。その後、Sitecoreクライアントにリダイレクトされます。これでSitecore Clientで認証されました。

    .. note:: ユーザーがSitecore Clientにアクセスする権限を持っていない場合は、SIサーバーのログインページにリダイレクトされ、警告メッセージが表示されます。

    SIサーバーのログインページは、以前の/sitecore/loginのように見えますが、加えて、右上に現在許可されているユーザーが表示されるようになりました。

    * SIサーバーで既に認証されている場合。
      Sitecoreクライアントにリダイレクトされます。これでSitecore Clientで認証されました。

SIサーバーを使用して、ID、アクセス、およびリフレッシュトークンを要求して使用します。Sitecore Identityは、これらのトークンを使用してSitecoreサービスへのリクエストを認証します。Sitecore ユーザーは、Sitecore XP のインスタンスが実行されていなくても、個別にホストされているさまざまなサイトやサービスにサインインすることができます。

SI は、Sitececore クライアントのデフォルトのログインページを置き換えるため、ブラウザのブックマークを https://{domain}/sitecore/login から https://{domain}/sitecore に更新する必要があります。

.. note:: SIを有効にすると、古い/sitecore/ログインページがユーザーをリダイレクトします。ただし、:doc:`古いログインページを使用することはできます <understanding-sitecore-authentication-behavior-changes>` 。

.. tip: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/sitecore-identity.html

.. toctree::
   :hidden:

   sitecore-identity-structure
   sitecore-identity-server-authentication
   configure-a-sitecore-instance-and-sitecore-identity-server
   understanding-sitecore-authentication-behavior-changes
   single-sign-out
   use-bearer-tokens-in-client-applications
   use-the-sitecore-identity-server-as-a-federation-gateway
   localize-the-ui