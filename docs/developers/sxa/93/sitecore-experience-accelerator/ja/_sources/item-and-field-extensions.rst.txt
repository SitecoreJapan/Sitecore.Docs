##################################
項目とフィールドの拡張子
##################################

このトピックでは、フィールド、アイテム プロパティ、およびフィールド プロパティに簡単にアクセスできるようにする SXA アイテム拡張機能について説明します。

*************************
アイテムプロパティ
*************************

i_item.children
==================

アイテムの子をアイテムリストとして返します。

サンプル
------------

アイテムの子要素をリンクのリストとしてレンダリングします。

.. code-block::

    {{ for i_child as i_datasource.children }}
    <a href="{{ i_child.url }}">{{ i_child.Title }}</a><br/>
    {{ end }}


i_item.display_name
=====================

アイテムの表示名を返します。

サンプル
------------

データソース項目の表示名をリンクのテキストとして使用して、コンポーネントのデータソースへのリンクをレンダリングします。

.. code-block::

    <a href="{{ i_datasource.url }}">{{ i_datasource.display_name }}</a>

i_item.id
==============

アイテムのIDを返します。

サンプル
------------

アイテムのIDを出力します。

.. code-block::

    {{ i_item.id }}


i_item.has_children
=======================

アイテムに子がある場合に true を返します。

サンプル
------------

現在のページに子がある場合、その子へのリンクをレンダリングします。

.. code-block::

    {{
        if i_page.has_children
            for i_child in i_page.children
                }}<a href="{{ i_child.url }}">{{ i_child.display_name }}</a><br/>{{
            end
        end  }}


i_item.has_layout
=======================

項目にレイアウト情報がある場合に true を返します。

サンプル
------------

子を持つページの子をレンダリングします。

.. code-block::

    {{
        for i_child in i_page.children
            if i_child.has_layout
                }}<a href="{{ i_child.url }}">{{ i_child.display_name }}</a><br/>{{
            end
        end
    }}


i_item.language
=======================

子を持つページの子をレンダリングします。

サンプル
------------

項目が英語でレンダリングされている場合は、その事実を示すテキストを出力します。

.. code-block::

    {{ if i_page.language == 'en' }}
    This page is rendered in English<br/>
    {{ end }}

i_item.media_url
=======================

アイテムのメディアURLを返します。

サンプル
------------

Promo コンポーネント画像へのダウンロードリンクをレンダリングします。

.. code-block::

    <a download href="{{ i_datasource.PromoIcon.media_url }}">{{ i_datasource.PromoIcon.name }}</a>

i_item.name
=======================

Promo コンポーネント画像へのダウンロードリンクをレンダリングします。

サンプル
------------

データ ソース項目の名前をリンクのテキストとして使用して、コンポーネントのデータ ソースへのリンクをレンダリングします。

.. code-block::

    <a href="{{ i_datasource.url }}">{{ i_datasource.name }}</a>


i_item.path
=======================

アイテムのパスを返します。

サンプル
------------

アイテムへのリンクは、/sitecore/contentの下にある場合にのみレンダリングされます。

    {{ if string.starts_with i_item.path "/sitecore/content" }}
    <a href="{{ i_item.url }}">{{ i_item.Title }}</a><br/>
    {{ end }}

i_item.parent
=======================

アイテムの親を返します。

サンプル
------------

データ ソース項目の親へのリンクをレンダリングします。

.. code-block::

    <a href="{{ i_item.parent.url }}">{{ i_item.parent.Title }}</a><br/>
    {{ end }}

i_item.template_id
=======================

アイテムテンプレートのIDを文字列で返します。

サンプル
------------

アイテムのIDを出力します。

    {{ i_item.template_id }}

i_item.template_name
=======================

アイテムテンプレートの名前を文字列で返します。

サンプル
------------

アイテムテンプレート名を返します。

.. code-block::

    {{ if i_item.template_name == "Gallery Image" }}
    ... render HTML for Gallery image
    {{end}}

i_item.url
=======================

サイトのLinkProviderを使用してアイテムのURLを返します。

サンプル
------------

データソース項目の表示名をリンクのテキストとして使用して、コンポーネントのデータソースへのリンクをレンダリングします。

.. code-block::

    <a href="{{ i_datasource.url }}">{{ i_datasource.display_name }}</a>

i_item.version
=======================

アイテムのバージョン番号を返します。

サンプル
------------

アイテムのバージョン番号を出力します。

.. code-block::

    {{ i_item.version }}


******************************************
アイテムフィールドの取得とレンダリング
******************************************

i_item.FieldName
=======================

テンプレート内のフィールドをレンダリングします。

サンプル
------------

アイテムオブジェクトにFieldNameを追加して、テンプレート内のフィールドをレンダリングします。

.. code-block::

    {{ i_item.Title }}

.. note:: i_item.FieldNameは、'.'、','、','、空白などの特殊文字を含むフィールドの後続リンクを許可しません。また、データ属性やフィールドのフォールバックもサポートしていません。このような場合は、sc_field関数を使用してください。


******************************************
フィールド拡張プロパティ
******************************************

i_item.Field.raw
=======================

フィールドの生の値を返します。例えば、JavaScriptで使用する項目や編集できないはずのボタンにIDをレンダリングしたい場合などです。

サンプル
------------

ボタン上のテキストをレンダリングします。

    <button type="button" class="btn btn-primary">{{ i_item.ButtonText.raw }}</button>

i_item.Field.target
=======================

アイテムへのリンクを格納できるフィールドで選択されたアイテムを返します。フィールドに複数の項目へのリンクを格納できる場合は、リストの最初の項目が返されます。フィールドが空の場合、この関数はヌル値を返します。

.. note:: i_item.Field.targetでは、'.'、','、空白などの特殊文字を含むフィールドへのリンクを許可していません。このような場合は、sc_follow関数を使用してください。

サンプル
------------

Promoコンポーネントからリンクされた商品の詳細情報をレンダリングします。

.. code-block::

    {{ i_linkedpage = i_item.PromoLink.target }}
    <h2>{{ i_linkedpage.Title }}</h2>
    <b>Content</b>:{{ i_linkedpage.Content }}

i_page.Field.targets
=======================

アイテムへのリンクを格納できるフィールドで選択されたアイテムの配列を返します。フィールドが空の場合、この関数は空のリストを返します。

.. note:: i_page.Field.targetでは、'.'、','、空白などの特殊文字を含むフィールドへのリンクを許可していません。このような場合は、sc_followmany関数を使用してください。

サンプル
------------

現在の製品に関連する製品をレンダリングします。

.. code-block::

    {{ for i_product in i_page.RelatedProducts.targets }}
    <h2>{{ i_product.Title }}</h2>
    <b>Content</b>:{{ i_product.Content }}
    {{ end }}

***********************************************
フィールドカスケードとフォールバックでの作業
***********************************************

流暢な記法でフィールドを使用する場合、フィールドのカスケードとフォールバックを使用して、より複雑なシナリオを簡素化することができます。.target ヘルパーを呼び出さなくても、流暢な記法でフィールドをカスケードリンクしてアイテムに到達することができます。例：i_item.FieldLinkingToItem.HelperOrFieldToRender。

例えば、記事ページのFirstNameという名前のフィールドをレンダリングするには、Authorという名前のフィールドの一部であるAuthor Infoという名前のフィールドをレンダリングします。

.. code-block::

    記事の作成者 {{ i_page.Author.FirstName }}

フィールド名を流暢な表記でカスケードすることで、複数のデータ テンプレートをサポートするテンプレートを作成できます。テンプレート化エンジンは、フィールドを左から右に解決してレンダリングしようとし、アイテム上で見つかった最初のフィールドをレンダリングします。

例えば、ページにAuthorフィールドがない場合、テンプレートエンジンは、i_pageから直接FirstNameフィールドをレンダリングしようとします。

.. tip:: 英語版 https://doc.sitecore.com/developers/sxa/93/sitecore-experience-accelerator/en/the-embedded-items-and-objects-in-the-scriban-context.html