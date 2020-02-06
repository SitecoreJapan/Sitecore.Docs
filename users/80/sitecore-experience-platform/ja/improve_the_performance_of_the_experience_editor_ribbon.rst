################################################################
エクスペリエンス エディター リボンのパフォーマンスを改善する
################################################################

エクスペリエンス エディター リボンのパフォーマンスを改善するために、Web アプリケーション キャッシュ (HTML 5 機能) を使用することができます。これによって、 js、 css、画像などすべてのリボンのリソースと、コマンドのリソースをキャッシュすることができます。

この章では、以下について説明します：

* Web アプリケーションのキャッシュを有効化する
* 実行時にリソースを含ませる

**********************************************
Web アプリケーションのキャッシュを有効化する
**********************************************

Web アプリケーションのキャッシュはデフォルトで無効化されています。有効化するには以下の手順を実施します：

* アイテムもしくはページのレイアウトのHTML タグにmanifest="/sitecore/shell/client/Sitecore/ExperienceEditor/Html5AppCache.ashx " を追加します。 例えば次のベースレイアウトファイル：\Website\sitecore\shell\client\Speak\Layouts\Layouts\SpeakLayout.cshtml

*****************************
実行時にリソースを含ませる
*****************************

Sitecore.ExperienceEditor.Speak.Caches.ResourcesCache クラスを使って、実行時にリソースを含ませることができます。このクラスには、リソースを登録することができる 3 つのメソッドがあります：

* Add (string url) - 指定されたリソースをキャッシュに追加します。
* RequireCss (RibbonComponentControlBase component, string category, string url) - component.Requires.Css(category, url) SPEAK と Add メソッドを使用します。
* RequireJs (RibbonComponentControlBase component, string category, string url) - component.Requires. Script (category, url) SPEAK と Add メソッドを使用します。

また、\Website\sitecore\shell\client\Sitecore\ExperienceEditor\WebAppCache.cache ファイルを使って追加のリソースを含ませることもできます。これは、独自のリソースを Web アプリケーションのキャッシュに含ませるだけで設定できます。

Web アプリケーションのキャッシュは、以下の時にリロードされます：

* Sitecore インスタンスが再起動された時。
* WebAppCache.cache ファイルが更新された時。
