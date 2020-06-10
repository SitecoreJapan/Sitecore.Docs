###################################################################
カスタムソース定義の作成
###################################################################

カスタム ソース定義を作成し、リスト マネージャを使用してそれらを管理することができます。リストとソースの間の関連付けは、少なくとも 1 つの連絡先がそのソースを使用してリストに追加されると自動的に作成されます。

カスタムソース定義を作成するには、以下の手順に従います。

1. スタート画面で、コンテンツエディタをクリックします。

2. Sitecore/System/Marketing Control Panel/Subscriptionsに移動します。

3. ツリービューで、サブスクリプションを右クリックし、テンプレートから挿入をクリックします。

4. Templates/System/Analytics フォルダを展開し、サブスクリプションのアイテムをクリックして、[挿入] をクリックします。

5. [コンテンツ] タブの [名前] フィールドに、連絡先リストのページに表示する名前を入力します。

6. 複数の連絡先リストにまたがってサブスクリプションを使用するには、[タグ] フィールドで共有値を定義します。このような場合は、ユーザーは、そのユーザーの名前を入力して、そのユーザーの名前を入力します。

7. 作成したサブスクリプション定義アイテムを展開します。

以下のAPIを使用して、カスタムのサブスクリプションソースを持つリストにコンタクトをサブスクライブすることができます。

.. code-block:: c#

    var contacts = new List<Contact>(); // List of contacts
    var subscriptionService = ServiceLocator.ServiceProvider.GetService<ISubscriptionService>();
    subscriptionService.Subscribe(contactListId, contacts, subscriptionSourceId);

subscriptionSourceId は、サブスクリプション定義アイテムの ID である。

.. tip:: 英語版 https://doc.sitecore.com/users/93/sitecore-experience-platform/en/create-a-custom-source-definition.html