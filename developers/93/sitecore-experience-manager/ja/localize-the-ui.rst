##########################################
UI のローカライズ
##########################################

Sitecore Identity (SI) サーバーは、ASP.NET Core サービスとミドルウェアを使用して、異なる言語や文化にローカライズします。SIサーバーが返すメッセージは、<application_root>localizationフォルダに保存します。ファイルの名前は、一般的な Sitecore ローカライゼーションファイル名フォーマット（languageName-cultureName.xml）で付けられます。

ローカライズ用の新しい言語を追加するには

1. ローカライズファイルをSIサーバー上の<application_root>\localizationフォルダにコピーします。

2. SIサーバーアプリケーションを再起動します。

SI サーバーは ASP.NET Core リクエスト・プロバイダーを使用して、各リクエストの言語と文化を選択します。各リクエストに対して、SI サーバーは、どのリクエスト カルチャを使用するかを決定できる以下のリストの最初のプロバイダを使用します。

* StoredQueryStringCultureProvider
* CookieRequestCultureProvider
* AcceptLanguageHeaderRequestCultureProvider

UI カルチャーがクエリ文字列に設定されている場合、StoredQueryStringCultureProvider プロバイダは言語とカルチャーを解決し、それをブラウザのクッキーに保存して後続のリクエストで使用します。

次の例では、特定のカルチャ（言語と地域）をスペイン語/メキシコ語に設定しています。

.. code-block::

    http://localhost:5000/?culture=es-MX

プロバイダが解決できない場合のデフォルトのリクエスト言語を指定できます。これは、Sitecore.identityServer.Host.xmlファイルのDefaultCulture設定によって制御されます。

.. code-block:: xml

    <Settings>
        <Sitecore>
            <IdentityServerHost>
            <DefaultCulture>en</DefaultCulture>
            </IdentityServerHost>
        </Sitecore>
    </Settings>

SI サーバーは、ローカリゼーションリソースを検索する際にカルチャーフォールバックを使用します。要求されたカルチャから開始します。これが見つからない場合は、そのカルチャの親カルチャに移動します。これは通常、常にではありませんが、ISO コードから国の記号を削除することを意味します。例えば、メキシコで話されているスペイン語の方言は es-MX です。親は es です。どの国にも固有ではないスペイン語です。


.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-manager/en/localize-the-ui.html