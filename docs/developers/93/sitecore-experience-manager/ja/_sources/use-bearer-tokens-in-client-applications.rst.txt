##############################################################
クライアントアプリケーションで bearer トークンを使用する
##############################################################

このトピックでは、ベアラートークン認証と Sitecore Identity サーバーを使用して、MVC クライアントから API に安全にアクセスする方法について説明します。

ベアラートークン認証には、以下の 3 つの要素が含まれます。

* Sitecore Identity (SI) サーバー。SIサーバーは、デフォルトでJWT（JSON Web Token）形式のアクセストークンを発行します。現在、関連するすべてのプラットフォームは、JWTトークンの検証をサポートしています。アクセストークンとベアラートークンは同じものと考えることができます。
* API アプリケーション。
* MVCクライアントアプリケーション。アプリケーションは SI サーバにアクセストークンを要求し、それを使用して API にアクセスします。

このトピックで説明する手順では、これらの前提に基づいた例を使用します。

* SI サーバは https://localhost:44356/ で動作しています。例を使用する場合は、これを実際の URL に変更してください。
* MVC クライアントは id として MvcClient を持ち、SI サーバ上で次のように設定されています。

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <Settings>
    <Sitecore>
        <IdentityServer>
        <Clients>
            <SampleMvcClient>
            <ClientId>MvcClient</ClientId>
            <ClientName>Sample MVC client</ClientName>
            <AccessTokenType>0</AccessTokenType>
            <AllowOfflineAccess>true</AllowOfflineAccess>
            <AlwaysIncludeUserClaimsInIdToken>true</AlwaysIncludeUserClaimsInIdToken>
            <AccessTokenLifetimeInSeconds>120</AccessTokenLifetimeInSeconds>
            <IdentityTokenLifetimeInSeconds>120</IdentityTokenLifetimeInSeconds>
            <AllowAccessTokensViaBrowser>true</AllowAccessTokensViaBrowser>
            <RequireConsent>false</RequireConsent>
            <RequireClientSecret>false</RequireClientSecret>
            <AllowedGrantTypes>
                <AllowedGrantType1>client_credentials</AllowedGrantType1>
                <AllowedGrantType2>hybrid</AllowedGrantType2>
            </AllowedGrantTypes>
            <RedirectUris>
                <RedirectUri1>{AllowedCorsOrigin}/signin-oidc</RedirectUri1>
            </RedirectUris>
            <PostLogoutRedirectUris>
                <PostLogoutRedirectUri1>{AllowedCorsOrigin}/signout-callback-oidc</PostLogoutRedirectUri1>
            </PostLogoutRedirectUris>
            <AllowedCorsOrigins>
                <AllowedCorsOriginsGroup1>http://localhost:54567</AllowedCorsOriginsGroup1>
            </AllowedCorsOrigins>
            <AllowedScopes>
                <AllowedScope1>openid</AllowedScope1>
                <AllowedScope2>sitecore.profile</AllowedScope2>
                <AllowedScope3>sitecore.profile.api</AllowedScope3>
            </AllowedScopes>
            <UpdateAccessTokenClaimsOnRefresh>true</UpdateAccessTokenClaimsOnRefresh>
            </SampleMvcClient>
        </Clients>
        </IdentityServer>
    </Sitecore>
    </Settings>


*******************
APIを保護する
*******************

このセクションでは、SI サーバーとベアラートークン認証を使用して API を保護する基本的なシナリオを概説する。ASP.NET Core ベースの API を保護するには、DI で JWT ベアラ認証ハンドラを設定し、認証ミドルウェアをパイプラインに追加するだけです。

API を保護するには

1. ASP.NET Core Web API テンプレートを使用して Visual Studio で新規プロジェクトを作成し、起動プロファイルでアプリケーション URL を構成します。ここで使用する例では、URL として http://localhost:55600/ を使用して API を構成することを想定しています。この URL は、独自のソリューションで実際に使用する URL に置き換えてください。
2. プロジェクトにパッケージ参照を追加します。

.. code-block:: xml

    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
    </ItemGroup>
    </Project>

3. スタートアップクラスを設定します。

.. code-block:: C#

    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvcCore()
                .AddAuthorization()
                .AddJsonFormatters();

            services.AddAuthentication("Bearer")
                .AddJwtBearer("Bearer", options =>
                {
                    options.Authority = "https://localhost:44356";
                    options.RequireHttpsMetadata = false;

                    options.Audience = "sitecore.profile.api";
                });
        }

        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseAuthentication();
            app.UseMvc();
        }
    }

AddAuthenticationメソッドは、認証サービスを追加し、Bearerをデフォルトのスキームとして設定します。AddJwtBearer メソッドは、SI Server アクセストークン検証ハンドラを追加し、認証サービスがそれを使用できるようにします。UseAuthentication メソッドは、認証ミドルウェアをパイプラインに追加し、ホストへの呼び出しごとに認証が自動的に実行されるようにします。

4. APIプロジェクトに新しいコントローラを追加します。

.. code-block:: c#

    [Route("[controller]")]
    [Authorize]
    public class IdentityController : ControllerBase
    {
        [HttpGet]
        public IActionResult Get()
        {
            return new JsonResult(from c in User.Claims
                select new
                {
                    c.Type,
                    c.Value
                });
        }
    }

コントローラ (http://localhost:55600/identity) に移動すると、401 ステータスコードが返ってきます。これは、あなたの API がクレデンシャルを必要とし、SI サーバーによって保護されていることを示しています。

****************************
MVCクライアントの設定
****************************

MVC クライアントが保護された API を使用するように設定するには。

次の例では、http://localhost:54567/ を URL として API を構成したと仮定しています。この URL を、SI サーバー クライアント構成の AllowedCorsOriginsGroup1 ノードで、独自のソリューションで実際に使用している URL に置き換えます。

プロジェクトにパッケージ参照を追加します。

.. code-block:: xml

    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="[2.1.3]" />
        <PackageReference Include="Microsoft.AspNetCore.Authentication.Abstractions" Version="[2.1.1.0]" />
        <PackageReference Include="IdentityModel" Version="[3.8.0]" />
    </ItemGroup>
    </Project>

MVC アプリケーションのスタートアップクラスの ConfigureServices メソッドで、クッキーと OpenID Connect 認証のサポートを追加します。

.. code-block:: C#

    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();

            services.AddAuthentication(options =>
                {
                    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = "oidc";
                })
                .AddCookie(options =>
                {
                    options.ExpireTimeSpan = TimeSpan.FromMinutes(60);
                    options.Cookie.Name = "mvcimplicit";
                })
                .AddOpenIdConnect("oidc", options =>
                {
                    options.ClientId = "MvcClient";
                    options.Authority = "https://localhost:44356";
                    options.RequireHttpsMetadata = false;
                    options.GetClaimsFromUserInfoEndpoint = true;
                    options.ResponseType = "code token";

                    options.Scope.Clear();
                    options.Scope.Add("openid");
                    options.Scope.Add("sitecore.profile");
                    options.Scope.Add("offline_access");
                    options.Scope.Add("sitecore.profile.api");

                    options.SaveTokens = true;

                    options.TokenValidationParameters = new TokenValidationParameters
                    {
                        NameClaimType = JwtClaimTypes.Name,
                        RoleClaimType = JwtClaimTypes.Role,
                    };
                });
        }

        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseDeveloperExceptionPage();
            app.UseStaticFiles();
            app.UseAuthentication();
            app.UseMvcWithDefaultRoute();
        }
    }

AddAuthentication メソッドは、認証サービスを追加します。DefaultScheme には Cookies が指定されているため、クッキーがユーザを認証する主な方法です。DefaultChallengeScheme は oidc として指定されているため、ユーザーがログインするときは OpenID Connect スキームを使用しなければなりません。

AddCookie メソッドは、クッキーを処理できるハンドラを追加します。

AddOpenIdConnectメソッドは、OpenID Connectプロトコルを実行するハンドラを構成します。Authority プロパティは、SI サーバが信頼されていることを指定します。このクライアントは、ClientId プロパティで識別できます。SignInScheme メソッドは、OpenID Connect プロトコルが完了すると、クッキーハンドラを使用してクッキーを発行します。SaveTokens メソッドは、SI サーバからのトークンをクッキーに永続化します（後で必要になります）。ResponseTypeプロパティにコードトークンを指定します（これは実質的にハイブリッドフローを使用することを意味します）。

4. ConfigureメソッドのUseAuthenticationメソッドを呼び出して、リクエストごとに認証サービスが実行されるようにします。認証ミドルウェアをMVCの前にパイプラインに追加する必要があります。

5. 認証ハンドシェイクをトリガーにするには、ホームコントローラに[Authorize]属性を追加します。

.. code-block:: 

    [Authorize]
    public class Home : Controller
    {
        private static readonly HttpClient HttpClient = new HttpClient();
    
        public async Task<IActionResult> Index()
        {
            string accessToken = await HttpContext.GetTokenAsync("access_token");
            string refreshToken = await HttpContext.GetTokenAsync("refresh_token");

            return Content($"Current user: <span id=\"UserIdentityName\">{User.Identity.Name ?? "anonymous"}</span><br/>" +
                $"<div>Access token: {accessToken}</div><br/>" +
                $"<div>Refresh token: {refreshToken}</div><br/>"
                , "text/html");
        }
    }

現在、ブラウザでこのコントローラに移動すると、SIサーバへのリダイレクトが試みられ、認証に成功すると、このページが表示されます。

.. code-block::

    Current user: sitecore\Admin
    Access token: eyJhbG......L8A
    Refresh token: 4cdf3b4d873a65135553afdf420a47dbc898ba0c1c0ece2407bbbf2bde02a68b

6. このアクションをホームコントローラに追加して、ベアラートークンでAPIを呼び出すようにします。

.. code-block:: 

    [Route("/callapi")]
        public async Task<IActionResult> CallApi()
        {
            string accessToken = await HttpContext.GetTokenAsync("access_token");

            var request = new HttpRequestMessage(HttpMethod.Get, "http://localhost:55600/identity");
            request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            HttpResponseMessage response = await HttpClient.SendAsync(request);

            if (response.StatusCode != HttpStatusCode.OK)
            {
                return Content(response.ToString());
            }

            return Content($"{await response.Content.ReadAsStringAsync()}");
        }


7. access_token 引数を渡すことで、GetTokenAsync メソッドを使用して、HttpContext からベアラ (アクセス) トークンを取得します。このようにして、アクセストークンをリクエスト・ヘッダに追加します。

.. code-block::

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

http://localhost:54567/callapi urlに移動します。答えは以下のようになるはずです。

.. code-block:: 

    [
    {
    "type": "nbf",
    "value": "1543572239"
    },
    {
    "type": "exp",
    "value": "1543572359"
    },
    {
    "type": "iss",
    "value": "https://localhost:44356"
    },
    {
    "type": "aud",
    "value": "https://localhost:44356/resources"
    },
    {
    "type": "aud",
    "value": "sitecore.profile.api"
    },
    {
    "type": "client_id",
    "value": "MvcClient"
    },
    {
    "type": "name",
    "value": "sitecore\\Admin"
    },
    ........

呼び出し元の URL (http://localhost:55600/identity) を変更して、別の API、例えば Sitecore アイテムサービスなどを呼び出すことができます。

.. code-block:: json

    {
    "ItemID": "110d559f-dea5-42ea-9c1c-8a5df7e70ef9",
    "ItemName": "Home",
    "ItemPath": "/sitecore/content/Home",
    "ParentID": "0de95ae4-41ab-4d01-9eb0-67441b7c2450",
    "TemplateID": "76036f5e-cbce-46d1-af0a-4143f9b557aa",
    "TemplateName": "Sample Item",
    "CloneSource": null,
    "ItemLanguage": "en",
    "ItemVersion": "1",
    "DisplayName": "Home",
    "HasChildren": "False",
    "ItemIcon": "/temp/iconcache/network/16x16/home.png",
    "ItemMedialUrl": "/-/icon/Network/48x48/home.png.aspx",
    "ItemUrl": "~/link.aspx?_id=110D559FDEA542EA9C1C8A5DF7E70EF9&amp;amp;_z=z",
    "Text": "&lt;p style.......e&lt;/a&gt;&lt;/p&gt;\r",
    "Title": "Sitecore Experience Platform"
    }

しばらくすると（SIサーバ上のクライアント設定でAccessTokenLifetimeInSecondsパラメータとして指定されている）、以下のような結果が得られます。

.. code-block::

    StatusCode: 401, ReasonPhrase:
    'Unauthorized', Version: 1.1, Content:
    System.Net.Http.HttpConnection+HttpConnectionResponseContent, Headers:
    {
    Server:
    Kestrel
    
    WWW-Authenticate: Bearer error="invalid_token",
    error_description="The token is expired"
    X-SourceFiles:
    =?UTF-8?B?Qzpcclxfd1xpc1xzYW1wbGVzXEFwaVxpZGVudGl0eQ==?=
    X-Powered-By:
    ASP.NET
    Date: Fri, 30
    Nov 2018 07:42:20 GMT
    
    Content-Length: 0
    }

つまり、アクセストークンの有効期限が切れているので、新しいアクセストークンを取得する必要があります。次のセクションでは、その方法を説明します。

**************************************************
リフレッシュトークンとアクセストークンの交換
**************************************************

SI サーバーには、プログラムでトークンを要求するために使用するトークンエンドポイントがあります。このサーバは OpenID Connect および OAuth 2.0 トークン要求パラメータのサブセットをサポートしています。OpenID のドキュメントに完全なリストがあります。

トークンを要求するには

1. ホームコントローラに次のアクションを追加します。

.. code-block:: 

    [Route("/exchange")]
    public async Task<IActionResult> Exchange()
    {
        var disco = await DiscoveryClient.GetAsync("https://localhost:44356");
        if (disco.IsError) throw new Exception(disco.Error);

        var tokenClient = new TokenClient(disco.TokenEndpoint, "MvcClient", "secret");
        var rt = await HttpContext.GetTokenAsync("refresh_token");
        var tokenResult = await tokenClient.RequestRefreshTokenAsync(rt);

        if (!tokenResult.IsError)
        {
            var expiresAt = (DateTime.UtcNow + TimeSpan.FromSeconds(tokenResult.ExpiresIn)).ToString("o", CultureInfo.InvariantCulture);

            var authService = HttpContext.RequestServices.GetRequiredService<IAuthenticationService>();
            AuthenticateResult authenticateResult = await authService.AuthenticateAsync(HttpContext, null);
            AuthenticationProperties properties = authenticateResult.Properties;

            properties.UpdateTokenValue(OpenIdConnectParameterNames.RefreshToken, tokenResult.RefreshToken);
            properties.UpdateTokenValue(OpenIdConnectParameterNames.AccessToken, tokenResult.AccessToken);
            properties.UpdateTokenValue(OpenIdConnectParameterNames.ExpiresIn, expiresAt);

            await authService.SignInAsync(HttpContext, null, authenticateResult.Principal, authenticateResult.Properties);

            return Redirect("/");
        }

        return BadRequest();
    }

TokenClient クラスのインスタンスを使用して、SI サーバに新しいトークンを要求します。この要求の前に有効なリフレッシュ・トークンが必要です。成功したリクエスト (RequestRefreshTokenAsync) の後、このリフレッシュ・トークンは無効になり、クッキー内のリフレッシュ・トークンとアクセス・トークンを新しいもので更新する必要があります。

2. AuthenticateAsyncメソッドを呼び出して認証プロパティを取得します。UpdateTokenValueメソッドはトークンを更新し、プロパティ内の有効期限のタイムスタンプも更新し、最後にSignInAsyncメソッドは認証クッキーを保存します。

これでGetTokenAsyncメソッドは更新されたアクセスまたは更新されたトークンを返します。



.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/use-bearer-tokens-in-client-applications.html