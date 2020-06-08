#########################################################################
フェデレーションゲートウェイとしてSitecore Identityサーバーを使用する
#########################################################################

`IdentityServer4 <https://identityserver4.readthedocs.io/en/release/>`_ をベースにしているため、Sitecore Identity (SI) サーバーを、1 つ以上の外部 ID プロバイダ (またはサブプロバイダ、インナープロバイダと呼ばれることもあります) へのゲートウェイとして使用することができます。 `IdentityServer4 フェデレーションゲートウェイ <http://docs.identityserver.io/en/latest/topics/federation_gateway.html>`_ に、この概念の詳細が記載されています。

サブプロバイダーを構成すると、SI サーバーのログイン画面にこのプロバイダーのログインボタンが表示されます。

SI サーバーに付属している Azure AD ID プロバイダーを有効にする必要があります。この方法については、「{SI_server_root_folder}s\Sitecore.Plugin.IdentityProvider.AzureAd\Config\readme.txt」を参照してください。

.. note:: `OpenID ConnectとAzure Active Directoryを使用してWebアプリケーションへのアクセスを許可 <https://docs.microsoft.com/en-us/azure/active-directory/azuread-dev/v1-protocols-openid-connect-code>`_ することで、SIサーバーと連携するようにAzure ADを構成することができます。

.. note:: このエラーが表示された場合 "リクエストで指定された返信URLは、アプリケーションに設定された返信URLと一致しません: "が表示された場合は、アプリケーションマニフェストのreplyUrlsセクションで https://[SI Server host name]/signin-oidc URLを使用してください。

*********************************************************
Sitecore Identityサーバーでサブプロバイダーを設定する
*********************************************************

SIサーバにサブプロバイダを追加するには

1. Visual Studio で新しいプラグインを作成します。

    a. クラス・ライブラリ（.NET Standard）C#プロジェクトを作成します。
    b. リポジトリのルートにglobal.jsonファイルを作成します。

    .. code-block:: 

        {
            "msbuild-sdks". {
                "Sitecore.Framework.Runtime.Build". “1.1.0”
            }
        }

    特定のバージョンを指定します。

    c. csprojファイルを編集します: Projectノードの後に<Sdk Name="Sitecore.Framework.Runtime.Build" />を追加します。
    d. 必要な他の参照を追加します。コマンドを有効にするためのSitecore.Framework.Runtime.Commands、ランタイム抽象化の使用を有効にするためのSitecore.Framework.Runtime.Abstractions、プラグインにすでにある機能の上にビルドする場合は他のプラグインへの参照を追加します。

2. このプロバイダの指示に従って優先サブプロバイダのサービスを設定し、SignInScheme 設定を idsrv.external として指定します。

.. note:: AddOpenIdConnect などの認証ミドルウェアを使用するには、Microsoft.AspNetCore.Authentication.AuthenticationBuilder 型のオブジェクトが必要です。プラグインでは、services.AddAuthentication() の代わりに新しい Microsoft.AspNetCore.Authentication.AuthenticationBuilder(services) を使用します。

3. 構成にサブプロバイダを登録します。SI_server_root_folder}S\sitecore\Sitecore.Plugin.IdentityProvider.AzureAdConfig\Sitecore.Plugin.IdentityProvider.AzureAd.xmlファイルに例があります。
4. オプションで、Sitecore インスタンスにサブプロバイダを登録します。

**************************************************
Sitecoreインスタンスにサブプロバイダを登録する
**************************************************

デフォルトでは、Sitecore インスタンスは、SI サーバー (Sitecore.Owin.Authentication.IdentityServer.config 構成ファイルの SitecoreIdentityServer 名) の 1 つの外部 ID プロバイダのみを知っており、明示的に指定しない限り、サブプロバイダについては知りません。

サブプロバイダは、次のような状況で指定します。

* SitecoreIdentityServer 自体ではなく、他のサイトにサブプロバイダをマッピングしたい場合。たとえば、SI サーバーはシェルサイトと管理者サイトにマップされていますが、SI サーバーで構成されている Azure AD プロバイダを Web サイトにマップしたいとします。
* GetSignInUrlInfoPipelineパイプラインを使用して、特定のサブプロバイダのサインインURLを取得して、ユーザーをサブプロバイダのログインページに直接リダイレクトしたい（SIログインページをスキップして）とします。

.. note::

    新しいログインエンドポイントをサブプロバイダーで利用するためには、Sitecoreにサブプロバイダーを登録する必要がありますが、これはオプションです。SIサーバーに組み込まれたAzure ADのサブプロバイダーを有効にし、シェルサイトのloginPage属性にこのURL $(loginPath)shell/SitecoreIdentityServer/IdS4-AzureAdを指定すると、シェルユーザーはAzure ADを使用して認証し、SIサーバーのログインページをスキップすることができます。

    SIサーバーにAzure ADを登録するには、AuthenticationSchemeの設定値をIdS4-AzureAdに指定します。

Sitecoreにサブプロバイダーを登録するには 

* sitecore/federatedAuthentication/identityProvidersノードにidentityProviderノードを追加します。この構成ノードは、name パラメータの命名規則に従う必要があります。名前パラメータは以下の形式にする必要があります。gateway_identity_provider]/[AuthenticationScheme]は、sitecoreが直接通信するIDプロバイダであり、AuthenticationSchemeは、gateway_identity_providerで構成したサブIDプロバイダ（例えば、IdS4-AzureAd）の認証スキームです。

  考慮しなければならない制限がいくつかあります。

    * TriggerExternalSignOut プロパティと Transformations プロパティは gateway_identity_provider ノードから継承され、それらをオーバーライドすることはできません。
    * このサブプロバイダを有効にするには、gateway_identity_provider プロバイダの Enabled プロパティを Enabled に設定する必要があります。

App_Config\Sitecore.Owin.Authentication.IdentityServer.config構成ファイルには、Azure ADサブプロバイダを登録する例があります。

****************************
クレームの変換を設定する
****************************

外部IDプロバイダごとにクレーム変換を設定することができます。それらはSitecoreが既に持っているものと似ていますが、以下の理由からSIサーバー側でクレーム変換を行う必要があります。

* SIサーバーと他のSIクライアント（Sitecore自体ではないもの）との間の通信にSitecoreインスタンスが存在しない。例えば、Sitecore以外のアプリケーションが外部プロバイダとしてSIサーバーを利用している場合、そのアプリケーションは、SIサーバーで正規化されていない限り、正規化されたクレームを受け取ることができません。
* SIサーバは、SIクライアントがスコープを使用して実際に要求したクレームのみを返します。そのため、SIクライアントは、未知のクレームを受け取ることはありません。

デフォルトでは、Sitecore Identityは以下のカスタムスコープで動作します。IDリソースとしてsitecore.profile、APIリソースとしてsitecore.profile.apiです。

.. note:: 

    sitecore/pipelines/owin.identityProviders/processor[id=SitecoreIdentityServer]/scopes)で、Sitecore に要求されたスコープを変更することができます。

    Sitecore.Plugin.Authentication.OpenIdConnectパッケージを使用するアプリケーションの場合は、{application_root_folder}sitecoreSitecore.Plugin.Authentication.OpenIdConnectConfig\openIdConnect.xmlファイルを確認してください。設定パスは、Sitecore:Authentication:OpenIdConnectOptions:Scope:...です。

.. note:: デフォルトのスコープ定義は、{SI_server_root_folder}sitecitecore.Plugin.IdentityServerConfig\\Sitecore.IdentityServer.xmlファイルに記載されています。

sitecore.profileとsitecore.profile.apiスコープの両方には、以下のようなクレームが含まれています。

* 名前
* 電子メール
* ロール
* http://www.sitecore.net/identity/claims/isAdmin
* http://www.sitecore.net/identity/claims/originalIssuer

ユーザーにロールを与えるために

* 特定の着信クレームを適切なロール・クレームにマッピングします。たとえば、90e5a2e5-4e3f-4f25-8beb-1238052fda8e Azure ADグループをsitecoreに変換するには、Sitecoreの\Authorロールを使用します。

.. code-block:: xml

    <AzureGroupTransformation type="Sitecore.Plugin.IdentityProviders.DefaultClaimsTransformation, Sitecore.Plugin.IdentityProviders">
                    <SourceClaims>
                                    <Claim1 type="groups" value="90e5a2e5-4e3f-4f25-8beb-1238052fda8e" />
                    </SourceClaims>
                    <NewClaims>
                                    <Claim1 type="role" value="sitecore\Author" />
                    </NewClaims>
    </AzureGroupTransformation>

.. note:: 同じ名前で複数のノードを追加しないでください。複数のノードが必要な場合は、AzureGroupTransformation1、AzureGroupTransformation2などのノードに名前を付けます。

ユーザーを管理者にするには

* http://www.sitecore.net/identity/claims/isAdmin を追加し、値を true に設定します（Sitecore で管理者ユーザーであることは、特定のロールを持つことではありません）。例えば、a9bb60b6-40d2-4e2a-88b8-0e47ee2d078e Azure AD グループのすべてのユーザーを Sitecore 管理者ユーザーにするには、以下のようにします。

.. code-block::

    <AzureADUserToAdminUser type="Sitecore.Plugin.IdentityProviders.DefaultClaimsTransformation, Sitecore.Plugin.IdentityProviders">
                    <SourceClaims>
                                    <Claim1 type="groups" value="a9bb60b6-40d2-4e2a-88b8-0e47ee2d078e" />
                    </SourceClaims>
                    <NewClaims>
                                    <Claim1 type="http://www.sitecore.net/identity/claims/isAdmin" value="true"/>
                    </NewClaims>
    </AzureADUserToAdminUser>

次に、Configuration:sitecore:federatedAuthentication:propertyInitializer:maps node に、Sitecore 内のクレームをマッピングする。 application_root_folder}\App_Config\Sitecore.Owin.Authentication.IdentityServer.configファイルには、この例があります。

.. code-block::

    <propertyInitializer>
        <maps>
            <map name="set IsAdministrator" type="Sitecore.Owin.Authentication.Services.DefaultClaimToPropertyMapper, Sitecore.Owin.Authentication">
                <data hint="raw:AddData">
                    <source name="http://www.sitecore.net/identity/claims/isAdmin" value="true" />
                    <target name="IsAdministrator" value="true" />
                </data>
            </map>
        </maps>
    </propertyInitializer>

変換された名前と電子メールのクレームについても同様の手順を使用して、Sitecore の User プロパティにマッピングします。

次の例は、必須のクレーム変換が必要であることを示しています。

サブプロバイダが http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress クレームでアイデンティティを返してきた場合、どのSIクライアントでもそれを取得することはできません。この場合、2つのオプションがあります。

* SI サーバでは、クレーム変換を追加して http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress を電子メール・クレームにマッピングすることができます。SI サーバでは、クレーム変換を追加して、 を電子メール・クレームにマッピングできます。
* SIサーバに必要なクレームをつけて新しいスコープを登録し、クライアントにこのスコープのリクエストを許可し、クライアントがこのスコープをリクエストする。

Sitecoreが外部ユーザーを表すために永続的なユーザーを作成する場合、いくつかの制限があります。Sitecoreは、そのようなユーザーに対して以下の機能をサポートしていません。

1. 外部ユーザーのロールはSitecoreに保存されていないため、User Managerで外部ユーザーのロールを読み取ったり削除したりすることはできません。
2. このため、アクセスビューアを使用する。
3. ユーザーのパスワードを変更する。対応するIDプロバイダでパスワードを変更する必要があります。

************************
AzureのADを設定する
************************

Azure AD を Sitecore Identity サーバーで設定して使用する場合は、覚えておく必要があります。

* アプリケーション登録の[Web] - [認証]タブの[詳細設定]でIDトークンのチェックボックスをオンにします。

* アプリケーションマニフェストのgroupMembershipClaims設定の値をSecurityGroupに設定します。

* アプリケーション登録のレガシーバージョンでのみ、replyUrls を構成できます。代わりに replyUrlsWithType を使用してください。


.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/use-the-sitecore-identity-server-as-a-federation-gateway.html