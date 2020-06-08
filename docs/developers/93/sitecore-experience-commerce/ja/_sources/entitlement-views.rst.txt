##########################################
エンタイトルメントビュー
##########################################

次の図は、エンタイトルメントビューの階層を表示しています。

.. image:: images/15ed685fd2bdba.png
   :align: center
   :width: 400px
   :alt: プレースホルダ

次の表では、Entitlement Viewsについて説明します。

+-----------------------------+----------------------------------------------+
| View                        | 説明                                         |
+-----------------------------+----------------------------------------------+
| CustomerEntitlements        | 顧客のエンタイトルメントを表示します。       |
+-----------------------------+----------------------------------------------+
| CustomerEntitlementsDetails | 顧客のエンタイトルメントの詳細を表示します。 |
+-----------------------------+----------------------------------------------+
| OrderEntitlements           | 注文のエンタイトルメントを表示します。       |
+-----------------------------+----------------------------------------------+
| OrderEntitlementDetails     | 注文のエンタイトルメントの詳細を表示します。 |
+-----------------------------+----------------------------------------------+

以下のエンティティブロックが利用可能です。

* エンティティ ビュー

    * GetCustomerEntitlementsViewBlock
    * GetOrderEntitlementsViewBlock
    * PopulateCustomerEntitlementsViewActionsBlock
    * PopulateOrderEntitlementsViewActionsBlock

* エンティティ・アクション

    * DoActionDeleteEntitlementBlock

.. tip:: 英語版 https://doc.sitecore.com/developers/93/sitecore-experience-commerce/en/entitlements-policies.html
