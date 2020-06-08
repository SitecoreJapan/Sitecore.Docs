##########################################
Sitecore Identity の構造
##########################################

Sitecore Identity (SI) は、以下のコンポーネント間の相互作用を提供します。

* Sitecore Identityサーバー - OpenID Connectに準拠したセキュリティトークンサービス。
* Sitecore Identityクライアント - これらは、SIサーバーからセキュリティトークンを要求できる個々のアプリケーションです。Sitecoreインスタンス自体もSIクライアントです。

**********************************
Sitecore Identity サーバー
**********************************

SIサーバーは、IdentityServer4をベースにしたスタンドアロンのASP.NET Coreアプリケーションである。OpenID Connect に準拠したセキュリティトークンサービス（STS）として動作します。Sitecore のデプロイ時に別の Web サイトとしてデプロイされ、デフォルトの URL は https://{instanceName}.identityserver です。

.. note:: 
SIサーバーはHTTPSプロトコルの下でしか動作しませんが、SIサーバー構成で適切に構成されている限り、HTTPとHTTPSの両方の下でSIクライアントをサポートできます。

SI サーバーは、構成ファイルに一部の IdentityServer4 構成を公開します。高度な IdentityServer4 設定を行うには、ランタイムプラグインを使用し、依存性インジェクションを使用して IdentityServer4 設定を変更する必要があります。これにより、IdentityServer4 構成は完全に構成可能になります。

**********************************
Sitecore Identityクライアント
**********************************

SIクライアントとは、SIサーバを利用しているユーザーを認証するアプリケーションのことです。クライアントが SI サーバーを使用できるようになる前に、すべての SI クライアントを SI サーバーに登録する必要があります。SI クライアントは、セキュリティ・トークンを要求し、それを検証し、そのトークンからコンテキスト・ユーザを作成できます。あるいは、bearerトークンとして使用して、そのようなトークンを受け入れるように構成されている他のサービスに許可された要求を行うこともできます。クライアントの例としては、Web アプリケーション、ネイティブのモバイルアプリケーションやデスクトップアプリケーション、 サーバープロセスなどがあります。

Sitecore インスタンスに SI サーバーを構成するには、『\App_Config\Sitecore\Owin.Authentication.IdentityServer\Sitecore.Owin.Authentication.IdentityServer.config』構成ファイルを使用します。

Sitecoreインスタンスは、SIクライアントでもあり、デフォルトではSIサーバに登録されています。SI サーバは、sitecore/federatedAuthentication/identityProviders ノードの ID プロバイダであるため、Sitecore インスタンスは SI サーバについて知っています。

.. note:: 独自のプロジェクトでSIサーバーを利用した認証を行うことができます。Sitecore.Plugin.Authentication.OpenIdConnect NuGetパッケージを使用して、SIサーバー認証を有効にし、Sitecore ASP.NET Coreベースのプロジェクトにアクセストークンを要求できるようにします。Sitecore.Plugin.Authentication.JwtBearer NuGetパッケージを使用して、Sitecore ASP.NETコアベースのプロジェクトに対してベアラートークン認証を有効にします。

.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/sitecore-identity-structure.html