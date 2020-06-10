################################
フォームの設定
################################

フォーム要素ペインの設定タブで、フォームの一般的な設定を適用することができます。

***************
スタイリング
***************

スタイリングセクションのCSSスタイル欄では、フォーム全体のスタイルを設定するクラスを指定することができます。デザインタブのフォームフィールドレベルでCSSスタイルを設定することで、特定のフィールドに対してこの設定をオーバーライドすることができます。

***************
サムネイル
***************

新しいフォームを作成すると、デフォルトのフォームサムネイルが表示されます。フォームに別の画像を選択したい場合は、Sitecore フォーム ではサムネイルをアップロードすることができます (/sitecore/メディアライブラリ/System/Forms/Form)。

フォームのサムネイルを選択するには

1. メディアの選択フィールドで、3 つのドットをクリックします。
2. [メディアの選択] ダイアログ ボックスで、サムネイルとして使用する画像を選択し、[選択] をクリックします。左メニューから画像を見つけるか、フィルタや検索機能を使用することができます。または、[メディアをアップロード] をクリックして新しい画像をアップロードすることもできます。

***************
条件
***************

Web フォームに条件付きアクションを適用することができます。[条件の編集] をクリックして条件を追加します。

***************
詳細設定
***************

パフォーマンストラッキング、AJAX、ロボット検出を有効にするかどうかは、以下のフィールドで選択できます。

+----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field                | Description                                                                                                                                                                                                                                                                                      |
+----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Performance tracking | Select to record data about how the form performs. When you select this field, you can track user behavior for each form field and view data on the abandon rate, error rate, and average time used to complete the field on the Performance tab.                                                |
|                      | If this field is selected on the Settings tab, you can choose to deselect Performance tracking on a field level. If you do not select this field on the Settings tab, selecting Performance tracking on a specific field does not have an effect.                                                |
+----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| AJAX                 | Select to save form field data even before the form is submitted. If you select this field, only the form UI is reloaded when navigating from one form page to another form page or during submission. If this field is cleared, the whole web page is reloaded during navigation or submission. |
|                      | If AJAX is disabled on a multipage form, you must advise visitors not to use the browser's back button because this leads to data loss.                                                                                                                                                          |
+----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Robot detection      | Select to enable robot detection for the form. If you select this field, Sitecore Form blocks the form submission traffic that is identified as a bot by Sitecore Analytics. Form data that is identified as a bot is not saved to the database.                                                 |
+----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+


.. tip:: https://doc.sitecore.com/users/93/sitecore-experience-platform/en/the-form-settings.html

