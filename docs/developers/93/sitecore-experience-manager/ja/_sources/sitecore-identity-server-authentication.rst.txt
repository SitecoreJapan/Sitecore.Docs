##########################################
Sitecore Identityサーバー認証
##########################################

Sitecore Identity (SI) サーバーを使用して、ASP.NET メンバーシップ (Sitecore コアデータベースまたはセキュリティデータベース) からの標準 Sitecore クライアントユーザーや、外部プロバイダーからのユーザーにサインインすることができます。

********************************
Sitecoreユーザーのサインイン
********************************

SIサーバーのログインページで資格情報を提供して、Sitecoreユーザーとしてサインインします。

SI サーバーは `identityserver-contrib-membership <https://github.com/sma73648/identityserver-contrib-membership>`_ を使用します。このプロジェクトでは、ASP.NET 2.0 Membership データベースを、IdentityServer4 の Identity Server User Store として使用できます。Sitecore:IdentityServer:SitecoreMembershipOptions:ConnectionString 設定を使用して、Membership データベースへの接続文字列を構成します。依存関係インジェクションを使用して、SI サーバーをより高度にカスタマイズしたり、必要に応じて Membership を別のソリューションに置き換えることができます。

********************************
外部ユーザーにサインイン
********************************

SI サーバーは、1 つ以上の外部 ID プロバイダ（サブプロバイダまたはインナープロバイダ）へのゲートウェイとして使用できます。サブプロバイダを構成すると、SI サーバのログイン画面にログインボタンが表示されます。SIサーバーのログイン画面をバイパスして、ユーザーを直接サブプロバイダーのログイン画面にリダイレクトするログインリンクを作成することができます。

SI サーバーには、Azure AD ID プロバイダが含まれています。

詳細については、:doc:`フェデレーションゲートウェイ <use-the-sitecore-identity-server-as-a-federation-gateway>` を参照してください。

.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/sitecore-identity-server-authentication.html