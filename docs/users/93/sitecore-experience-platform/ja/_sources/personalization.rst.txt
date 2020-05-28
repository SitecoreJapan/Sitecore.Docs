####################################
パーソナライズ
####################################

パーソナライゼーションとは、所在地、性別、以前の訪問履歴などの連絡先の特性や行動に基づいて、ターゲットを絞った関連性の高いコンテンツを表示する方法です。パーソナライゼーションを使用すると、コンテンツの表示、非表示、調整などにより、適切なコンテンツを適切な連絡先に確実に届けることができます。

特に、パーソナライゼーションを使用すると、以下のようなことが可能です。

連絡先の地理的な場所に基づいて異なるコンテンツを表示する。

以前にフォームに記入した連絡先から登録フォームを非表示にする。

連絡先の参照サイトに基づいて、Web サイトのバナーのテキストを変更する。

パーソナライゼーションを設定するには、 :doc:`ルールセットエディタ<the-rule-set-editor>` を使用して、Sitecoreの特定のコンポーネントにルールとアクションを追加します。ルールセットエディタは、これらのロジックベースのルールを使用して、コンタクトまたはセグメントが条件を満たしているかどうかを判断し、それに応じてコンテンツを変更します。

.. image:: images/15eafd35e823bd.png
   :align: center
   :width: 400px
   :alt: パーソナライズ

訪問者がサイト内を移動するページに基づいてコンテンツをパーソナライズするには、コンテンツの :doc:`プロファイリング <content-profiling>` を設定することができます。

*********************************
パーソナライゼーションの最適化
*********************************

Webサイトでコンテンツテストを行うと、Sitecoreが :doc:`パーソナライズ提案 <personalization-suggestions>` を作成してくれます。提案を個別に確認し、適用することができます。パーソナライズ提案を適用すると、Sitecore は新しいパーソナライズルールを作成します。

:doc:`Activeパーソナライズ <using-the-active-personalized-experiences-list>` エクスペリエンスリストを使用して、すべてのサイトのパーソナライズ体験を持つすべてのページの概要を得ることができます。


.. tip:: 英語版 https://doc.sitecore.com/users/93/sitecore-experience-platform/en/personalization.html

.. toctree::
    :hidden:
    :maxdepth: 1

    walkthrough--personalizing-components
    the-rule-set-editor
    the-personalization-conditions
    ten-personalization-tactics
    conditional-renderings
    the-personalized-experience-dialog-box
