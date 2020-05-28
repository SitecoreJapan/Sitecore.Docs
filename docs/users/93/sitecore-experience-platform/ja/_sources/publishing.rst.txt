####################################
パブリッシュ
####################################

Sitecoreで新しいアイテムを編集したり作成したりする際には、ウェブサイトに表示される前に公開する必要があります。これにより、未完成のアイテムを保存し、そのアイテムをウェブサイトに公開するタイミングを正確に決めることができます。

Sitecoreで作業すると、保存したアイテムはマスターデータベースに保存されます。新しいアイテムや更新されたアイテムを公開すると、そのアイテムがマスターデータベースからパブリッシングターゲットにコピーされ、パブリッシングターゲットからWebサイトが起動されます。Sitecoreのデフォルトのパブリッシングターゲットは、Webデータベースです。

Webサイト全体を発行するか、単一のアイテムを発行するかを選択できます。

* :doc:`サイトパブリッシング <publish-a-website>` - コンテンツツリーのルートから開始して、ウェブサイト全体をパブリッシングします。コンテンツエディタまたはSitecoreデスクトップからサイトを発行することができます。
* :doc:`アイテムの公開 <publish-an-item-to-your-website>` - コンテンツ エディターまたはエクスペリエンス エディターで選択したアイテムを公開します。アイテムは、その祖先がすべて公開されている場合にのみ公開できます。アイテム公開では、選択したアイテムのサブアイテムと関連するアイテムをすべて含めることができます。


.. tip:: 英語版 https://doc.sitecore.com/users/93/sitecore-experience-platform/en/publishing.html

.. toctree::
    :hidden:
    :maxdepth: 1
    
    validate-an-item
    preview-a-webpage
    publish-an-item-to-your-website
    publish-a-website
    set-up-publishing-restrictions-for-an-item
    publishing-items-to-a-preview-publishing-target
    